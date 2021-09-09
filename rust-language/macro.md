# 巨集

## 簡介

“巨集”（macro）是Rust的一個重要特性。Rust的“巨集”（macro）是一種編譯器擴展，它的調用方式為`some_macro！（...）`。巨集調用與普通函式呼叫的區別可以一眼區分開來，凡是巨集調用後面都跟著一個驚嘆號。

巨集也可以通過`some_macro！[...]`和`some_macro！{...}`兩種語法調用，只要括弧能正確匹配即可。

```rust
fn main(){
    println!("hello world 1");
    println!["hello world 2"];
    println!{"hello world 3"};
}
```

* 首先，Rust的巨集在調用的時候跟函數有明顯的語法區別；
* 其次，巨集的內部實現和外部調用者處於不同名字空間，它的訪問範圍嚴格受限，是通過參數傳遞進去的，我們不能隨意在巨集內訪問和改變外部的程式碼。
* C/C++中的巨集只在預處理階段起作用，因此只能實現類似文本替換的功能。而Rust中的巨集在語法解析之後起作用，因此可以獲取更多的上下文資訊，而且更加安全。

## 實現編譯階段檢查

使用巨集，我們可以在編譯階段分析這個字串常量和對應參數，確保它符合約定。另外一個常見的場景是，利用巨集來檢查規則運算式的正確性。

```rust
fn main() {
    // error: 2 positional arguments in format string, 
    // but no arguments were given
    println!("number1 {} number2 {}");
}
```

## 實現編譯期計算

```rust
// 可以列印出當前原始程式碼的檔案名，
// 以及當前程式碼的行數。這些資訊都是純編譯階段的資訊。
fn main() {
    // file src/main.rs line 4 
    println!("file {} line {} ", file!(), line!());
}
```

## 實現自動程式碼生成





## Format格式

Rust中還有一系列的巨集，都是用的同樣的格式控制規則，如format！write！writeln！等。詳細文件可以參見標準庫文件中[std::fmt](https://doc.rust-lang.org/std/fmt/index.html)模組中的說明。

```rust
fn main() {
    println!("{}", 1); // 1, 預設用法,列印Display
    println!("{:o}", 9); // 11, 八進制
    println!("{:x}", 255); // ff, 十六進位 小寫
    println!("{:X}", 255); // FF, 十六進位 大寫
    println!("{:p}", &0); // 指針
    println!("{:b}", 15); // 1111，二進位
    println!("{:e}", 10000f32); // 1e4, 科學計數(小寫)
    println!("{:E}", 10000f32); // 1E4, 科學計數(大寫)
    println!("{:?}", "test"); // 列印Debug
    println!("{:#?}", ("test1", "test2")); // 帶換行和縮進的Debug列印
    println!("{a} {b} {b}", a = "x", b = "y"); // 具名引數
}
```

