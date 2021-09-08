# 字串

## 簡介

Rust的字串有點複雜，主要是跟所有權有關。Rust的字串涉及兩種類型，一種是`&str`，另外一種是`String`。

## &str

str是Rust的內置類型。&str是對str的借用。**Rust的字串內部預設是使用utf-8編碼格式的。而內置的char類型是4位元組長度的，存儲的內容是Unicode Scalar Value**。所以，Rust裡面的字串不能視為char類型的陣列，而更接近u8類型的陣列。

```rust
fn main() {
    // 因為字串是 UTF-8 串流，所以並不支援使用索引來存取
    let s = "hello";
    println!("The first letter of s is {}", s[0]); // ERROR!!!
}
```

實際上str類型有一種方法：`fn as_ptr（&self）->*const u8`。它內部無須做任何計算，只需做一個強制類型轉換即可。

這樣設計有一個缺點，就是不能支援O（1）時間複雜度的索引操作。如果我們要找一個字串s內部的第n個字元，不能直接通過s\[n\]得到，這一點跟其他許多語言不一樣。在Rust中，這樣的需求可以通過下面的語句實現：

```rust
fn main() {
    let hachiko = "忠犬ハチ公";

    for b in hachiko.as_bytes() {
        print!("{}, ", b);
    }
    // 229, 191, 160, 231, 138, 172, 227, 131, 143, 227, 131, 129, 229, 133, 172,
    println!("");

    for c in hachiko.chars() {
        print!("{}, ", c);
    }
    // 忠, 犬, ハ, チ, 公,

    println!("");

    // 可以使用以下的方法達到類似於索引的功能
    let dog = hachiko.chars().nth(1); // kinda like hachiko[1]
    println!("{:?}", dog); // Some('犬')
}
```



