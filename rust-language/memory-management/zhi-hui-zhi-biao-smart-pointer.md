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

## Box

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
* 你想寫簡單的 singly linked list。
* 你需要做 dynamic dispatch，例如 dyn Trait（former Trait Object）。

## Cell與RefCell

用來打破 borrow checker 的「共享不可變，可變不共享」規則。讓 immutable reference 也能修改。

兩者修改 immutable reference 是透過「interior mutability」。

* &#x20;Cell 透過所有權轉移修改內部的 value。&#x20;
* RefCell 藉由 runtime borrow checker 操作指標。 兩者皆不會額外在 heap 配置空間存資料。

### 繼承可變性(Inherited mutability)

一個資料結構內部欄位的可變性取決於「變數是可變的綁定或是不變的綁定」。可<mark style="color:red;">變性一旦決定就會影響全體，每個欄位都會「繼承」相同的可變性</mark>。

```rust
fn main() {
    struct S(u8, u8);
    let mut s1 = S(1, 2);
    s1.0 += 2; // s1 is mutable, so s1.0 is also mutable.
    let s2 = S(1, 2);
    s2.0 += 2; // Compile failed. s2 is immutable.
}
```

### 內部可變性(Interior mutability)

若一個型別可以透過 shared reference 來修改其內部狀態，則我們稱之有內部可變性 ，這會透過 UnsafeCell 這個黑盒子實作。

UnsafeCell 是 safe Rust 中少數可以無視「共享不可變，可變不共享」的型別，Cell 與 RefCell 都是其衍生型別。

### Cell實作

Cell 的結構體不包含其他 metadata，直接就是透過 UnsafeCell 儲存 value。

```rust
pub struct Cell<T: ?Sized> {
    value: UnsafeCell<T>,
}
```

Cell 對不同型別的操作維持一個共識：「轉移整個資料的所有權」。例如 get、set 皆是直接 copy 或是取代原本的值。

```rust
use std::cell::Cell;
fn main() {
    let c = Cell::new(5);
    println!("{:?}", c);    // 5
    c.set(10); // discard 5 and value set to 10
    println!("{:?}", c);    //10
    c.get(); // 10 (a copy but not a reference)
    c.replace(7); // 10 (returns the replaced value)
    println!("{:?}", c);    // 7   
}
```

### RefCell實作

RefCell 是會動態檢查 borrow checker rule 的型別，比 Cell 多一個 borrow 的欄位，動態紀錄引用的情形。

```rust
pub struct RefCell<T: ?Sized> {
    borrow: Cell<BorrowFlag>, // 
    value: UnsafeCell<T>,
}
```

RefCell 提供 borrow\_mut() 與 borrow() 兩個方法，動態產生不同的 reference。

```rust
use std::cell::RefCell;
fn main() {
    let c = RefCell::new(5); // create a RefCell
    *c.borrow_mut() += 5; // mutably borrow and mutate interior value
    assert_eq!(&*c.borrow(), &10); // immutably borrow to check the value
}
```

比較特別的是，`RefCell::borrow` 與 `RefCell:borrow_mut` 的回傳值並非 `&T` 與 `&mut T`，而是帶有實作細節的 Ref 與 RefMut。這個特性讓 RefCell 無法設計出函式庫等級的優美介面，反而更適合 application 層的實際應用。

### 什麼時候該用 Cell 或 RefCell

* 你需要一個對外 immutable，但內部狀態可變的資料結構。
* 你需要動態建立多個可變的 alias。
* 你想要變更 Rc 多個指標底層的資料狀態。

## Rc (reference call)

單執行緒的引用計數指標，用來共享配置在 heap 上的資料。

* 內部紀錄引用資料的指標數量，當最後一個 Rc 指標銷毀時，資料也隨風而去。
* &#x20;因為共享，所以禁止任何 mutation（但可從內部可變性繞過）。&#x20;
* 可能發生迴圈引用，導致記憶體洩漏，此時須使用 Weak 弱引用。&#x20;
* 非原子操作，所以無法在執行緒間傳遞，但可用 Arc 原子引用計數指標。&#x20;
* 類似 C++std::shared\_ptr。

```rust
pub struct Rc<T: ?Sized> {
    ptr: NonNull<RcBox<T>>,
    phantom: PhantomData<T>,
}
struct RcBox<T: ?Sized> {
    strong: Cell<usize>,
    weak: Cell<usize>,
    value: T,
}
```

建立 Rc 並共享引用（複製 Rc pointer）。

```rust
use std::rc::Rc;
fn main() {
    let obj = Rc::new((1, 2, 3));
    let another_obj = Rc::clone(&obj);
    assert_eq!(obj, another_obj);
}
```

升級 `Weak` -> `Rc` ，與降級 `Rc` -> `Weak。`

```rust
use std::rc::Rc;
fn main() {
    let five = Rc::new(5);
    let weak_five = Rc::downgrade(&five);
    let strong_five: Option<Rc<_>> = weak_five.upgrade();
    assert!(strong_five.is_some());
    // Destroy all strong pointers.
    drop(strong_five);
    drop(five);
    assert!(weak_five.upgrade().is_none());
}
```

### Rc 如何管理 weak 與 strong reference

```rust
unsafe impl<#[may_dangle] T: ?Sized> Drop for Rc<T> {
    fn drop(&mut self) {
        unsafe {
            self.dec_strong();
            if self.strong() == 0 {
                // destroy the contained object
                ptr::drop_in_place(self.ptr.as_mut());
                // remove the implicit "strong weak" pointer now that we've
                // destroyed the contents.
                self.dec_weak();
                if self.weak() == 0 {
                    Global.dealloc(self.ptr.cast(), Layout::for_value(self.ptr.as_ref()));
                }
            }
        }
    }
}
```

### 什麼時候該用 Rc

* 你需要共享一堆引用，但不確定哪個引用的生命週期會先結束。
* 你的資源不足，但需要一個 GC（Rc + RefCell = 窮人的 GC）。

## Cow (copy on write)

Clone On Write 的智慧指標，修改指標指向的資料時，才會複製一份資料。

* Cow 是一個 enum，會是 borrowed 或 owned data 其中一種。&#x20;
* Cow 的 T 需實作 `ToOwned trait`，可視為需要能被 clone。

```rust
pub enum Cow<'a, B: ?Sized + 'a>
    where B: ToOwned
{
    /// Borrowed data.
    Borrowed &'a B), 
    /// Owned data.
    Owned(<B as ToOwned>::Owned),
}
```

事實上，除了 trait實現外，Cow 只有 `into_owned` 與 `to_mut` 兩個方法。

```rust
use std::borrow::Cow;
fn main() {
    let s = "Hello world!";
    let cow = Cow::Borrowed(s); // 引用 s
    assert_eq!(
        cow.into_owned(), // 透過 `into_owned()` 轉換成 owned data
        String::from(s)
    );
    let mut cow = Cow::Borrowed("foo");
    cow.to_mut().make_ascii_uppercase(); // 利用 to_mut() 複製一份資料
    assert_eq!(
        cow,
        Cow::Owned(String::from("FOO")) as Cow<str> // 已與原始資料不同
    );
}
```

### 什麼時候該用 Cow

* 你遇到 clone 的效能瓶頸，希望減少 clone 的次數。
* 你不想要處理多重指標的問題，但也不想要全部都 clone。
* 你想要避免 Mutex 互斥鎖效能不彰的問題。
