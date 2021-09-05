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

另外，針對一個類型，我們可以直接對它impl來增加成員方法，無須trait名字（可視為該類型特有的匿名trait）。

```rust
// 實現Circle類型的匿名trait
impl Circle {
    fn get_radius(&self) -> f64 {
        self.radius
    }
}
```

