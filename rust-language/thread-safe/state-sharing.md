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

