# 基本資料類型

## bool

布林類型（bool）代表的是“是”和“否”的二值邏輯。它有兩個值：true和false。一般用在邏輯運算式中，可以執行AND、OR、NOT等運算。

```rust
fn main() {
    let x = true;
    let y: bool = !x; // not運算
    
    //false, logical and,帶短路功能
    let z = x && y; 
    println!("{}", z);
    
    //true, logical or,帶短路功能
    let z = x || y; 
    println!("{}", z);
    
    // false, bitwise and,不帶短路功能
    let z = x & y; 
    println!("{}", z);
    
    // true, bitwise or,不帶短路功能
    let z = x | y; 
    println!("{}", z);
    
    // true, bitwise xor,不帶短路功能
    let z = x ^ y; 
    println!("{}", z);

    // 一些比較運算運算式的類型就是bool類型
    let z: bool = 2 < 3;
    println!("{}", z);
}
```

