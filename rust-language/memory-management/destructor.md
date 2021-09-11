# 解構函數\(destructor\)

## 簡介

所謂“解構函數”（destructor），是與“構造函數”（constructor）相對應的概念。**“構造函數”是物件被創建的時候調用的函數，“解構函數”是物件被銷毀的時候調用的函數**。

Rust中沒有統一的“構造函數”這個語法，物件的構造是直接對每個成員進行初始化完成的，我們一般將物件的創建封裝到普通靜態函數中（通常命名為new）。

相對於構造函數，**解構函數有更重要的作用。它會在物件消亡之前由編譯器自動調用，因此特別適合承擔物件銷毀時釋放所擁有的資源的作用**。比如，Vec類型在使用的過程中，會根據情況動態申請記憶體，當變數的生命週期結束時，就會觸發該類型的解構函數的調用。

在解構函數中，我們就有機會將所擁有的記憶體釋放掉。在解構函數中，我們還可以根據需要編寫特定的邏輯，從而達到更多的目的。解構函數不僅可以用於管理記憶體資源，還能用於管理更多的其他資源，如檔案、鎖、socket等。

**在C++中，利用變數生命週期綁定資源的使用週期，已經是一種常用的程式設計慣例。此手法被稱為RAII（Resource Acquisition Is Initialization）**。在變數生命週期開始時申請資源，在變數生命週期結束時利用解構函數釋放資源，從而達到自動化管理資源的作用，很大程度上減少了資源的洩露和誤用。在Rust中編寫“解構函數”的辦法是impl [std::ops::Drop](https://doc.rust-lang.org/std/ops/trait.Drop.html)。

```rust
pub trait Drop {
    fn drop(&mut self);
}
```

Drop trait允許在物件即將消亡之時，自行調用指定程式碼。我們來寫一個自帶解構函數的類型。

```rust
struct D(i32);
impl Drop for D {
    fn drop(&mut self) {
        println!("destruct {}", self.0);
    }
}
fn main() {
    let _x = D(1);
    println!("construct 1");
    {
        let _y = D(2);
        println!("construct 2");
        println!("exit inner scope");
        // _y生命週期結束，呼叫dtor
    }
    println!("exit main function");
    // 離開主函數後，_x的生命週期結束，呼叫dtor
}

/*
construct 1
construct 2
exit inner scope
destruct 2
exit main function
destruct 1
*/
```

。變數`_y`的生存期是內部的大括弧包圍起來的作用域（scope），待這個作用域中的程式碼執行完之後，它的解構函數就被調用；變數`_x`的生存期是整個main函數包圍起來的作用域，待這個函數的最後一條語句執行完之後，它的解構函數就被調用。

**對於具有多個區域變數的情況，解構函數的調用順序是：先構造的後解構，後構造的先解構。因為區域變數存在於一個“堆疊”的結構中，要保持“先進後出”的策略**。

## 資源管理

在創建變數的時候獲取某種資源，在變數生命週期結束的時候釋放資源，是一種常見的設計模式。這裡的資源，不僅可以包括記憶體，還可以包括其他向作業系統申請的資源。比如我們經常用到的File類型，會在創建和使用的過程中向作業系統申請打開檔案，在它的解構函數中就會去釋放檔案。所以，RAII手法是比GC更通用的資源管理手段，GC只能管理記憶體，RAII可以管理各種資源。

```rust
use std::fs::File;
use std::io::Read;
fn main() {
    // 在File的解構函數中已經定義如果退出時，會自動關檔
    let f = File::open("/target/file/path");
    if f.is_err() {
        println!("file is not exist.");
        return;
    }
    let mut f = f.unwrap();
    let mut content = String::new();
    let result = f.read_to_string(&mut content);
    if result.is_err() {
        println!("read file error.");
        return;
    }
    println!("{}", result.unwrap());
}
```

再比如標準庫中的各種複雜資料結構（如Vec, LinkedList, HashMap等），它們管理了很多在堆上動態分配的記憶體。它們也是利用“解構函數”這個功能，在生命終結之前釋放了申請的記憶體空間，因此無須像C語言那樣手動調用free函數

## 主動解構

一般情況下，區域變數的生命週期是從它的聲明開始，到當前語句塊結束。然而，我們也可以手動提前結束它的生命週期。請注意，**使用者主動調用解構函數是非法的**，我們怎樣才能讓區域變數在語句塊結束前提前終止生命週期呢？辦法是調用標準庫中的[`std::mem::drop`](https://doc.rust-lang.org/std/mem/fn.drop.html)函數。

```rust
use std::mem::drop;
fn main() {
    let mut v = vec![1, 2, 3]; // <--- v的生命週期開始
    // v.drop();    // 非法的操作
    drop(v); // ---> v的生命週期結束
    v.push(4); // 錯誤的調用
}
```

標準庫中的std::mem::drop函數是Rust中最簡單的函數，因為它的實現為“空”：

```rust
#[inline]
pub fn drop<T>(_x: T) {}
```

drop函數不需要任何的函數體，只需要參數為“值傳遞”即可。**將物件的所有權移入函數中，什麼都不用做，編譯器就會自動釋放掉這個物件了**。因為這個drop函數的關鍵在於使用move語義把參數傳進來，使得變數的所有權從調用方移動到drop函數體內，**參數類型一定要是T，而不是&T或者其他參考類型**。

函數體本身其實根本不重要，**重要的是把變數的所有權move進入這個函數體中，函式呼叫結束的時候該變數的生命週期結束，變數的解構函數會自動調用，管理的記憶體空間也會自然釋放**。這個過程完全符合前面講的生命週期、move語義，無須編譯器做特殊處理。事實上，我們完全可以自己寫一個類似的函數來實現同樣的效果，只要保證參數傳遞是move語義即可。

因此，對於Copy類型的變數，對它調用std::mem::drop函數是沒有意義的。

```rust
use std::mem::drop;
fn main() {
    let x = 1_i32;
    println!("before drop {}", x);
    // 基本類型因為有實現copy trait
    // 因此變數傳入drop時是copy而不是move
    // 因此不會解構
    drop(x);    
    println!("after drop {}", x);
}
```

變數遮蔽（Shadowing）不會導致變數生命週期提前結束，它不等同於drop。

```rust
use std::ops::Drop;
struct D(i32);
impl Drop for D {
    fn drop(&mut self) {
        println!("destructor for {}", self.0);
    }
}
fn main() {
    let x = D(1);
    println!("construct first variable");
    let x = D(2);
    println!("construct second variable");
}

/*
construct first variable
construct second variable
destructor for 2
destructor for 1
*/
```

這裡函式呼叫的順序為：

* 先創建第一個x，再創建第二個x
* 退出函數的時候，先解構第二個x，再解構第一個x。

由此可見，在第二個x出現的時候，雖然將第一個x遮蔽起來了，但是第一個x的生命週期並未結束，它依然存在，直到函數退出。這也說明了，雖然這兩個變數綁定了同一個名字，但在編譯器內部依然將它們視為兩個不同的變數。

另外還有一個小問題需要注意，那就是底線這個特殊符號。請注意：如果你用底線來綁定一個變數，那麼這個變數會當場執行解構，而不是等到當前語句塊結束的時候再執行。底線是特殊符號，不是普通識別字。

```rust
use std::ops::Drop;
struct D(i32);
impl Drop for D {
    fn drop(&mut self) {
        println!("destructor for {}", self.0);
    }
}
fn main() {
    let _x = D(1);
    let _ = D(2);   // 立即解構
    let _y = D(3);
}

/*
destructor for 2
destructor for 3
destructor for 1
*/
```

之所以是這樣的結果，是因為用底線綁定的那個變數當場就執行了解構，而其他兩個變數等到語句塊結束了才執行解構，而且解構順序和初始化順序剛好相反。所以，如果需要利用RAII實現某個變數的解構函數在退出作用域的時候完成某些功能，千萬不要用底線來綁定這個變數。

最後，要注意區分，`std::mem::drop()`函數和`std::ops::Drop::drop()`方法。

1. `std::mem::drop()`函數是一個獨立的函數，不是某個類型的成員方法，它由程式設計師主動調用，作用是使變數的生命週期提前結束；
2. `std::ops::Drop::drop()`方法是一個trait中定義的方法，當變數的生命週期結束的時候，編譯器會自動調用，手動調用是不允許的。
3. `std::mem::drop<T>（_x:T）`的參數類型是`T`，採用的是move語義；`std::ops::Drop::drop（&mut self）`的參數類型是&mut Self，採用的是可變借用。在解構函式呼叫過程中，我們還有機會讀取或者修改此物件的屬性。

## Question：如果在變數生命週期結束後，出現異常，變數的解構函數是否會執行?

\[todo\]



