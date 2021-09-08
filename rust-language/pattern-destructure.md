# 模式解構\(pattern destructure\)

## 簡介

Rust中模式解構功能設計得非常美觀，它的原則是：構造和解構遵循類似的語法，我們怎麼把一個資料結構組合起來的，我們就怎麼把它拆解開來。

注意這裡的“Destructure”和“Destructor”是完全不同的兩個單詞，代表完全不同的含義。

* “Destructure”的意思是把原來的結構肢解為單獨的、局部的、原始的部分；
* “Destructor”是指“解構器”，是一個與“構造器”相對應的概念，是在物件被銷毀的時候調用的。

```rust
let tuple = (1_i32, false, 3f32);    // 建構tuple
let (head, center, tail) = tuple;    // 解構tuple至各變數
```

```rust
// struct tuple
struct T1(i32, char);
// struct
struct T2 {
    item1: T1,
    item2: bool,
}
fn main() {
    // 建構T2 structure
    let x = T2 {
        item1: T1(0, 'A'),
        item2: false,
    };
    // 解構x至T2 struct對應的變數中
    let T2 {
        item1: T1(value1, value2),
        item2: value3,
    } = x;
    println!("{} {} {}", value1, value2, value3);
}
```

