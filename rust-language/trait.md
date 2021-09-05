# trait

在Rust中，trait這一個概念承擔了多種職責。在中文裡，trait可以翻譯為“特徵”“特點”“特性”等。由於這些詞區分度並不明顯，因此不翻譯trait這個詞，以避免歧義。trait中可以包含：函數、常量、類型等。

## 成員函數\(membership function\)

trait中可以定義函數，稱為該trait的成員函數。該函數可直接實作，等到imp時再實作即可。

```rust
trait Shape {
    fn area(&self) -> f64;
}
```

所有的trait中都有一個隱藏的**類型Self（大寫S），代表當前這個實現了此trait的具體類型**。trait中定義的函數，也可以稱作關聯函數（associated function）。

函數的第一個參數如果是Self相關的類型，且命名為self（小寫s），這個參數可以被稱為“receiver”（接收者）。

Rust中Self（大寫S）和self（小寫s）都是關鍵字，大寫S的是類型名，小寫s的是變數名，一定要注意區分。

* trait中具有receiver參數的函數，我們稱為“方法”（method）可以通過變數實例使用小數點來調用。
* trait中沒有receiver參數的函數，我們稱為“靜態函數”（static function），可以通過類型加雙冒號：：的方式來調用。
* **在Rust中，函數和方法沒有本質區別**。

對於第一個self參數，常見的類型有`self:Self`、`self:&Self`、`self:&mut Self`等類型。對於以上這些類型，Rust提供了一種簡化的寫法，我們可以將參數簡寫為`self`、`&self`、`&mut self`。

self參數只能用在第一個參數的位置。請注意“變數self”和“類型Self”的大小寫不同。

```rust
trait T {
    // 參數為self, 類型為Self
    fn method1(self: Self);
    // 參數為self, 類型為&Self
    fn method2(self: &Self);
    // 參數為self, 類型為&mut Self
    fn method3(self: &mut Self);
}
// 上下兩種寫法等價
trait T {
    fn method1(self);
    fn method2(&self);
    fn method3(&mut self);
}
```

trait中可以包含方法的預設實現。如果這個方法在trait中已經有了方法體，那麼在針對具體類型實現的時候，就可以選擇不用重寫。當然，如果需要針對特殊類型作特殊處理，也可以選擇重新實現來“override”預設的實現方式。

比如，在標準庫中，反覆運算器Iterator這個trait中就包含了十多個方法，但是，其中只有`fn next（&mut self）->Option<Self::Item>`是沒有預設實現的。其他的方法均有其預設實現，在實現反覆運算器的時候只需挑選需要重寫的方法來實現即可。

## 實現trait

我們可以為某些具體類型實現（impl）這個trait。

假如我們有一個結構體類型Circle，它實現了這個trait，程式碼如下：



```rust
trait Shape {
    fn area(self: &Self) -> f64;
}

struct Circle {
    radius: f64,
}

impl Shape for Circle {
    // Self 類型就是 Circle
    // self 的類型是 &Self,即 &Circle
    fn area(&self) -> f64 {
        // 訪問成員變數,需要用 self.radius
        std::f64::consts::PI * self.radius * self.radius
    }
}
fn main() {
    let c = Circle { radius: 2_f64 };
    // 第一個參數名字是 self,可以使用小數點語法調用
    println!("The area is {}", c.area());
}
```

### 類型的內在方法

另外，針對一個類型，我們可以直接對它impl來增加成員方法，無須trait名字（可視為該類型特有的匿名trait）。用這種方式定義的方法叫作這個類型的“內在方法”（inherent methods）。

```rust
// 實現Circle類型的匿名trait
impl Circle {
    fn get_radius(&self) -> f64 {
        self.radius
    }
}
```

### 為trait實現trait

```rust
trait Shape {
    fn area(&self) -> f64;
}
trait Round {
    fn get_radius(&self) -> f64;
}
struct Circle {
    radius: f64,
}
impl Round for Circle {
    fn get_radius(&self) -> f64 {
        self.radius
    }
}
// 注意這裡是 impl Trait for Trait
// Round不是類型而是trait，因此要加dyn
impl Shape for dyn Round {
    fn area(&self) -> f64 {
        std::f64::consts::PI * self.get_radius() * self.get_radius()
    }
}
fn main() {
    // Circle實現trait Round, 而trait Round實現Shape
    // 但是Circle不可直接使用Shape的method, 
    // 因為self的類型不同
    let c = Circle { radius: 2f64 };
    // 編譯錯誤
    // c.area();
    let b = Box::new(Circle { radius: 4f64 }) as Box<dyn Round>;
    // 編譯正確
    b.area();
}

```

### 靜態方法

沒有receiver參數的方法（第一個參數不是self參數的方法）稱作“靜態方法”。靜態方法可以通過`Type::FunctionName()`的方式調用。

需要注意的是，即便我們的第一個參數是Self相關類型，只要變數名字不是self，就不能使用小數點的語法調用函數。

```rust
// struct tuple
struct T(i32);
impl T {
    // 這是一個靜態方法，
    // 因為第一個參數的名字不是self
    fn func(this: &Self) {
        println! {"value {}", this.0};
    }
}
fn main() {
    let x = T(42);
    // x.func(); 小數點方式調用是不合法的
    T::func(&x);
}
```

在標準庫中就有一些這樣的例子。Box的一系列方法`Box::into_raw(b:Self)`,`Box::leak(b:Self)`，以及Rc的一系列方法`Rc::try_unwrap(this:Self)`,`Rc::downgrade(this:&Self)`，都是這種情況。

它們的receiver不是self關鍵字，這樣設計的目的是強制使用者用`Rc::downgrade(&obj)`的形式調用，而禁止`obj.downgrade()`形式的調用。**這樣程式碼表達出來的意思更清晰，不會因為`Rc<T>`裡面的成員方法和T裡面的成員方法重名而造成誤解問題**。

trait中也可以定義靜態函數。下面以標準庫中的`std::default::Default` trait為例，介紹靜態函數的相關用法

```rust
// 上面這個trait中包含了一個default()函數，它是一個無參數的函數，
// 返回的類型是實現該trait的具體類型。Rust中沒有“構造函數”的概念。
// Default trait實際上可以看作一個針對無參數構造函數的統一抽象。
pub trait Default {
    fn default() -> Self;
}

impl<T> Default for Vec<T> {
        fn default() -> Vec<T> {
            Vec::new()
        }
    }
```

跟C++相比，在Rust中，定義靜態函數沒必要使用static關鍵字，因為它把self參數顯式在參數列表中列出來了。

作為對比，C++裡面成員方法預設可以訪問this指標，因此它需要用static關鍵字來標記靜態方法。Rust不採取這個設計，主要原因是self參數的類型變化太多，不同寫法語義差別很大，選擇顯式聲明self參數更方便指定它的類型。

## 擴展類型方法

我們還可以利用trait給其他的類型添加成員方法，哪怕這個類型不是我們自己寫的。

比如，我們可以為內置類型i32添加一個方法：

```rust
trait Double {
    fn double(&self) -> Self;
}
// 為內建類型實現trait
impl Double for i32 {
    fn double(&self) -> i32 {
        *self * 2
    }
}
fn main() {
    // 可以像成员方法一样调用
    let x: i32 = 10.double();
    println!("{}", x);
}
```

即使實作的類型不是在當前的專案中聲明的，我們依然可以為它增加一些成員方法。

但我們也不是隨隨便便就可以這麼做的，Rust對此有一個規定。在聲明trait和impl trait的時候，**Rust規定了一個Coherence Rule（一致性規則）或稱為Orphan Rule（孤兒規則）：impl塊要麼與trait的聲明在同一個的crate中，要麼與類型的聲明在同一個crate中**。

