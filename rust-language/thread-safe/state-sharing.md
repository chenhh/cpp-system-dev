# 狀態共享

## Arc

Arc是Rc的執行緒安全版本。它的全稱是“Atomic reference counter”。注意第一個單詞代表的是atomic而不是automatic。它強調的是“原子性”。它跟Rc最大的區別在於，引用計數用的是原子整數類型。

```rust
use std::sync::Arc;
use std::thread;
fn main() {
    let numbers: Vec<_> = (0..100u32).collect();
    // 引用計數指標,指向一個 Vec
    let shared_numbers = Arc::new(numbers);
    // 迴圈創建 10 個執行緒
    for _ in 0..10 {
        // 複製引用計數指標,所有的 Arc 都指向同一個 Vec
        let child_numbers = shared_numbers.clone();
        // move修飾閉包,上面這個 Arc 指標被 move 進入了新執行緒中
        thread::spawn(move || {
            // 我們可以在新執行緒中使用 Arc,讀取共用的那個 Vec
            let local_numbers = &child_numbers[..];
            // 繼續使用 Vec 中的資料
        });
    }
}
```

如果不小心把Rc用在了多執行緒環境，直接是編譯錯誤，根本不會引發多執行緒同步的問題。如果不小心把Arc用在了單執行緒環境也沒什麼問題，不會有bug出現，只是引用計數增加或減少的時候效率稍微有一點降低。

## mutex

根據Rust的“共用不可變，可變不共用”原則，Arc既然提供了共用引用，就一定不能提供可變性。所以，Arc也是唯讀的，它對外API和Rc是一致的。如果我們要修改怎麼辦？同樣需要“內部可變性”。這種時候，我們需要用執行緒安全版本的“內部可變性”，如Mutex和RwLock。

```rust
use std::sync::Arc;
use std::sync::Mutex;
use std::thread;
const COUNT: u32 = 1000000;
fn main() {
    let global = Arc::new(Mutex::new(0));
    // 為了避免把global這個引用計數指標move進入閉包，
    // 所以在外面先提前複製一份，然後將複製出來的這個指標傳入閉包中
    let clone1 = global.clone();
    let thread1 = thread::spawn(move || {
        for _ in 0..COUNT {
            let mut value = clone1.lock().unwrap();
            *value += 1;
        }
    });
    let clone2 = global.clone();
    let thread2 = thread::spawn(move || {
        for _ in 0..COUNT {
            let mut value = clone2.lock().unwrap();
            *value -= 1;
        }
    });
    thread1.join().ok();
    thread2.join().ok();
    println!("final value: {:?}", global);
}
```

如果當前Mutex已經是“有毒”（Poison）的狀態，它返回的就是錯誤。什麼情況會導致Mutex有毒呢？當Mutex在鎖住的同時發生了panic，就會將這個Mutex置為“有毒”的狀態，以後再調用lock（）都會失敗。這個設計是為了panic safety而考慮的，主要就是考慮到在鎖住的時候發生panic可能導致Mutex內部資料發生混亂。所以這個設計防止再次進入Mutex內部的時候訪問了被破壞掉的資料內容。如果有需要的話，使用者依然可以手動調用PoisonError::into\_inner（）方法獲得內部資料。

而MutexGuard類型則是一個“智慧指標”類型，它實現了DerefMut和Deref這兩個trait，所以它可以被當作指向內部資料的普通指標使用。MutexGuard實現了一個解構函數，通過RAII手法，在解構函數中調用了unlock（）方法解鎖。因此，使用者是不需要手動調用方法解鎖的。

