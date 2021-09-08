# 字串

## 簡介

Rust的字串有點複雜，主要是跟所有權有關。Rust的字串涉及兩種類型，一種是`&str`，另外一種是`String`。

## &str

str是Rust的內置類型。&str是對str的借用。**Rust的字串內部預設是使用utf-8編碼格式的。而內置的char類型是4位元組長度的，存儲的內容是Unicode Scalar Value**。所以，Rust裡面的字串不能視為char類型的陣列，而更接近u8類型的陣列。

```rust
fn main() {
    // 因為字串是 UTF-8 串流，所以並不支援使用索引來存取
    let s: &str = "hello";
    println!("The first letter of s is {}", s[0]); // ERROR!!!
}
```

實際上str類型有一種方法：`fn as_ptr（&self）->*const u8`。它內部無須做任何計算，只需做一個強制類型轉換即可。

這樣設計有一個缺點，就是不能支援O（1）時間複雜度的索引操作。如果我們要找一個字串s內部的第n個字元，不能直接通過s\[n\]得到，這一點跟其他許多語言不一樣。在Rust中，這樣的需求可以通過下面的語句實現：

```rust
fn main() {
    let hachiko: &str = "忠犬ハチ公";

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

它的時間複雜度是O（n），因為utf-8是變長編碼，如果我們不從頭開始過一遍，根本不知道第n個字元的位址在什麼地方。但是，綜合來看，選擇utf-8作為內部預設編碼格式是缺陷最少的一種方式了。相比其他的編碼格式，它有相當多的優點。

\[T\]是DST類型，對應的str是DST類型。&\[T\]是陣列切片類型，對應的&str是字串切片類型：

```rust
fn main() {
    let hachiko: &str = "hello";
    // substr中取range可行是因為hackiko中存放的都是ascii字元
    // 如果存放的是utf8的字串的話，range取法會有問題, 
    // 因為range是取bytes而不是chars
    let substr : &str = &hachiko[2..];
    println!("{}", substr);
}
```

&str為胖指標：

```rust
fn main() {
    // &str為fat pointer，2個usize，第一個存ptr address, 第二個存array長度
    println!("Size of pointer: {}", std::mem::size_of::<*const ()>()); //8
    println!("Size of &str : {}", std::mem::size_of::<&str>()); //16
}
```



