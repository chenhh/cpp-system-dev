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

bool類型運算式可以用在if/while等運算式中，作為條件運算式。

```rust
if a >= b {
  ...
} else {
  ...
}
```

## char

字元類型由char表示。它可以描述任何一個符合unicode標準的字元值。在程式碼中，單個的字元字面量用單引號包圍。

```rust
use std::mem;
fn main(){
    //char以單引號包住, 可以直接嵌入任何 unicode 字符
    //char佔用4 bytes的空間
    let love = '愛'; 
    println!("love={}, size={}", love, mem::size_of_val(&love));
    
    let c1 = '\n';   // 分行符號, 佔用4bytes
    println!("{}, size={}", c1, mem::size_of_val(&c1));
    
    let c2 = '\x7f'; // 8 bit 字元變數, 佔用4bytes
    println!("{}, size={}", c2, mem::size_of_val(&c2));
    
    let c3 = '\u{7FFF}'; // unicode字元, 佔用4bytes
    println!("{}, size={}", c3, mem::size_of_val(&c3));
    
    /* 可以使用一個字母b在字元或者字串前面，
     * 代表這個字面量存儲在u8類型陣列中，
     * 這樣佔用空間比char型陣列要小一些。
     */
    let x :u8 = 1;
    println!("{}, size={}", x, mem::size_of_val(&x));   // 1 byte
    
    let y :u8 = b'A';
    println!("{}, size={}", y, mem::size_of_val(&y));   // 1 byte
    
    let s :&[u8;5] = b"hello";
    println!("{:?}, size={}", s, mem::size_of_val(&s)); // 8 bytes
    
    let r :&[u8;14] = br#"hello \n world"#;
    println!("{:?}, size={}", r, mem::size_of_val(&r)); // 8 bytes
}
```

