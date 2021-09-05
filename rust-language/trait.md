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
    fn method1(self: Self);
    fn method2(self: &Self);
    fn method3(self: &mut Self);
}
// 上下兩種寫法等價
trait T {
    fn method1(self);
    fn method2(&self);
    fn method3(&mut self);
}
```

