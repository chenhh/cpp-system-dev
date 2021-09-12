# 借用和生命週期

## 生命週期\(lifetime\)

一個變數的生命週期就是它從創建到銷毀的整個過程。

```rust
fn main() {
    let v = vec![1, 2, 3, 4, 5]; // --> v 的生命週期開始
    {
        let center = v[2];       // --> center 的生命週期開始
        println!("{}", center);
    }                            // <-- center 的生命週期結束
    println!("{:?}", v);
}                                // <-- v 的生命週期結束

```

