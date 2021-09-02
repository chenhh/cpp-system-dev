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

## 整數類型

各種整數類型之間的主要區分特徵是：有符號/無符號與佔據空間大小，未指明整數類型時預設為i32。



| 整數類型 | 有符號 | 無符號 |
| :--- | :--- | :--- |
| 8-bit | i8 | u8 |
| 16- bit | i16 | u16 |
| 32-bit | i32 | u32 |
| 64-bit | i64 | u64 |
| 128-bit | i128 | u128 |
| pointer size | isize | usize |

關於各個整數類型所佔據的空間大小，在名字中就已經表現得很明確了，Rust原生支持了從8位元到128位元的整數。

**需要特別關注的是isize和usize類型。它們佔據的空間是不定的，與指標佔據的空間一致**，與所在的平臺相關。如果是32位元系統上，則是32位大小；如果是64位元系統上，則是64位大小。在C++中與它們相對應的類似類型是int\_ptr和uint\_ptr。

Rust的這一策略與C語言不同，C語言標準中對許多類型的大小並沒有做強制規定，比如int、long、double等類型，在不同平臺上都可能是不同的大小，這給許多程式師帶來了不必要的麻煩。相反，在語言標準中規定好各個類型的大小，讓編譯器針對不同平臺做適配，生成不同的程式碼，是更合理的選擇。

```rust
fn main() {
    let var1: i32 = 32;   // 十進位表示
    let var2: i32 = 0xFF; // 以0x開頭代表十六進位表示
    let var3: i32 = 0o55; // 以0o開頭代表八進制表示
    let var4: i32 = 0b1001; // 以0b開頭代表二進位表示
    // 32, 255, 45, 9
    println!("{}, {}, {}, {}", var1, var2, var3, var4);
    
    // 使用底線分割數字,不影響語義,但是極大地提升了閱讀體驗。
    let var5 = 0x_1234_ABCD; 
    println!("{}", var5); // 305441741
    
    // 純量後面可以跟尾碼，可代表該數字的具體類型，省略掉顯示類型標記
    let var6 = 123usize; // i6變數是usize類型
    let var7 = 0x_ff_u8; // i7變數是u8類型
    let var8 = 32;       // 不寫類型,預設為 i32 類型
    println!("{}, {}, {}", var6, var7, var8);
}
```

在Rust中，我們可以為任何一個類型增加\(實作\)方法，整數也不例外。如std中對所有整數類型都有實作[pow](https://doc.rust-lang.org/std/primitive.i32.html#method.pow)方法。

```rust

fn main() {
    let x: i32 = 9;
    println!("9 power 3 = {}", x.pow(3));
    
    // 可以不使用變數，直接對整數調用函數
    println!("9 power 3 = {}", 9_i32.pow(3));
}
```

