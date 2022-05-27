# 智慧指標(smart pointer)

## 如何使用 struct 模擬指標

必須做到兩件事：

* 確認 dereference 時可解開引用，取得底層值 -> 實作 Deref trait
* 確認指標結束生命週期時，會正確釋放資源 -> 實作 Drop trait

符合上述要件，並在 safe Rust 前提下實作的智慧指標，就可消弭絕大多數記憶體問題，例如 double free 或 wild pointer。

<mark style="background-color:red;">Smart Pointer in Rust ＝ Data structure + Deref + Drop</mark>

### Deref trait

* 用來多載解引用運運算元 \*（dereference）的 trait。&#x20;
* 有一個 required method `fn deref(&self) -> &Self::Target`&#x20;
* DerefMut 則是用在 `&mut` 的 dereference。&#x20;
* Rust 有很多 implicit dereference，實作 Deref trait 會簡單很多。

### Implicit dereference

為了讓大家方便寫程式，不用再區分變數(obj.method)、變數指標(obj->method)、指標解引用((\*obj).method)，Rust 幫我們統一介面，只要是函式傳參或方法呼叫，當：

* 對指向 value type 的指標操作時，就執行原本的動作。&#x20;
* 超過一層指標包裹 value 時，會先呼叫 obj.deref()，將 T 引用解為 U 的值。

```rust
use std::ops::Deref;
fn main() {
    let s = &String::from("123");
    assert_eq!(3, s.len());
    assert_eq!(3, s.deref().len());
    assert_eq!(3, (&s).len()); // s.deref().deref()
    assert_eq!(3, (&&s).len()); // s.deref().deref().deref()
    assert_eq!(3, (&&&&&&s).len()); // s.deref().deref().deref()...
}
```

### Drop trait

* 可視為物件的解構函式，當物件離開 scope 自動呼叫，可用來釋放資源。
* 為 RAII pattern 的資源管理機制。&#x20;
* Rustc 不能直接呼叫drop方法，必衝使用 `std::mem::drop`。 Rustc 不保證一定會呼叫（例如 FFI 不需要）。

```rust
fn main() {
    let v = vec![1, 2, 3];

    drop(v); // Explicitly drop.
    {
        let v = vec![1, 2, 3];
    } // Implicitly drop. Call drop(&v) when v is out of scope.
}
```

## std crate的智慧指標

Box、Rc、Arc、Cel、RefCell、Mutex、RwLock、Atomic\*、Vec、String ，以及在 std::collections 的集合型別。

### Box

在 heap 上配置空間儲存資料。

* 指向 heap 上的資料，Box變數本身配置在 stack 上，佔 1 usize 的空間。
* 保有 content 的 ownership，Box 生命週期結束會 drop 它的資料。
* 是最泛用的智慧指標。 類似 C++ std::unique\_ptr。

```rust
fn main() {
    // 將值放入box
    let val = 5u8;
    let boxed = Box::new(val);

    // 解引用
    let val2 = *boxed;
    println!("val2={val2}"); //5

    // 自動解引用
    implicit_deref(&boxed); // 5
}
fn implicit_deref(a: &u8) {
    println!("{a}");
}
```

### 什麼時候該用 Box

* 你需要儲存遞迴的資料，且無法靜態決定型別大小。
* 你需要轉移資料的所有權但想避免複製整個物件。
* 你需要將資料配置在 heap 上。
* 你需要一個 null pointer（Option\<Box>）。
* 你想寫簡單的 singly linked list
* 你需要做 dynamic dispatch，例如 dyn Trait（former Trait Object）
