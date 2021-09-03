# 巨集

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

