---
description: 數組
---

# 陣列

## 簡介

* 陣列是一個容器，它在一塊**連續空間記憶體**中，存儲了一系列的**同樣類型**的資料。
* 陣列中元素的佔用空間大小必須是**編譯期確定**的。
* 陣列本身所容納的元素個數也必須是編譯期確定的，執行階段不可變。如
* 果需要使用變長的容器，可以使用標準庫中的Vec/LinkedList等。
* 陣列類型的表示方式為**\[T:n\]**。其中T代表元素類型；n代表元素個數；它必須是編譯期常量整數；中間用分號隔開。
* 對陣列內部元素的訪問，可以使用中括弧索引的方式。Rust支援usize類型的索引的陣列，**索引從0開始計數**。

```rust
fn main() {
    // 固定長度的陣列
    let xs: [i32; 5] = [1, 2, 3, 4, 5];
    println!("{:?}", xs);
    // 所有的元素,可使用以下語法初始為同個的值
    // 類別可省略由編譯器自動推導
    let ys = [2; 10];
    println!("{:?}", ys);
}
```

在Rust中，對於兩個陣列類型，只有元素類型和元素個數都完全相同，這兩個陣列才是同類型的。陣列與指標之間不能隱式轉換。同類型的陣列之間可以互相賦值。

```rust
fn main() {
    let mut xs: [i32; 5] = [1, 2, 3, 4, 5];
    let ys: [i32; 5] = [6, 7, 8, 9, 10];
    xs = ys;    // 所有權轉移
    println!("new array {:?}", xs);
    
    let zs = [0; 6];
    // xs = zs; // error, 長度不同的陣列不可賦值
    
    let ds = [0_f32; 5];
    // xs = ds; // error, 類別不同的陣列不可賦值
}
```

### 陣列比較

```rust
fn main() {
    let v1 = [1, 2, 3];
    let v2 = [1, 2, 4];
    // element-wise comparison
    println!("{:?}", v1 < v2);
}
```

### 遍歷操作

```rust
fn main() {
    let v = [0_i32; 10];
    for i in &v {
        println!("{:?}", i);
    }
}
```

## 多維陣列

```rust
fn main() {
    // 3 * 2 array
    let v: [[i32; 2]; 3] = [[0, 0], [0, 0], [0, 0]];
    for i in &v {
        println!("{:?}", i);
    }
}
```

## 陣列切片\(slice\)

**對陣列取借用borrow操作，可以生成一個“陣列切片”（Slice）**。

陣列切片對陣列沒有“所有權”，我們可以把陣列切片看作專門用於指向陣列的指標，是對陣列的另外一個“視圖” \(view\)。比如，我們有一個陣列`[T:n]`，它的借用指標的類型就是`&[T;n]`。它可以通過編譯器內部魔法轉換為陣列切片類型`&[T]`。陣列切片實質上還是指標，它不過是在類型系統中丟棄了編譯階段定長陣列類型的長度資訊，而將此長度資訊存儲為運行期的值。

```rust
fn main() {
    fn mut_array(a: &mut [i32]) {
        a[2] = 5;
    }
    println!("size of &[i32; 3] : {:?}", std::mem::size_of::<&[i32; 3]>()); //8
    // fat pointer佔用了兩個pointer的空間 (64-bit OS pointer為8 bytes)
    println!("size of &[i32] : {:?}", std::mem::size_of::<&[i32]>());   // 16
    println!("size of i32 : {:?}", std::mem::size_of::<i32>());   // 4
    let mut v: [i32; 3] = [1, 2, 3];
    {
        let s: &mut [i32; 3] = &mut v;
        mut_array(s);
    }
    println!("{:?}", v);    // [1, 2, 5]
}
```

## DST與胖指標

Slice與普通的指標是不同的，它有一個非常形象的名字：胖指標（fat pointer）。與這個概念相對應的概念是“動態大小類型”（Dynamic Sized Type, DST）。

**所謂的DST指的是編譯階段無法確定佔用空間大小的類型。為了安全性，指向DST的指標一般是胖指標**。比如：對於不定長陣列類型`[T]`，有對應的胖指標`&[T]`類型；對於不定長字串`str`類型，有對應的胖指標`&str`類型；以及在後文中會出現的Trait Object；等等。

由於不定長陣列類型`[T]`在編譯階段是無法判斷該類型佔用空間的大小的，目前我們不能在堆疊上聲明一個不定長大小陣列的變數實例，也不能用它作為函數的參數、返回值。但是，指**向不定長陣列的胖指標的大小是確定的**，`&[T]`類型可以用做變數實例、函數參數、返回值。

胖指標內部的資料既包含了指向原始陣列的位址，又包含了該切片的長度。

```rust
fn raw_slice(arr: &[i32]) {
    unsafe {
        // 強制類型轉換, 以usize去切記憶體
        let (val1, val2): (usize, usize) = std::mem::transmute(arr);
        println!("Value in raw pointer:");
        // fat pointer第一個值存原始pointer的地址
        println!("value1: {:x}", val1); // 7ffe95900c9c
        // fat pointer第二個值存原始pointer的長度
        println!("value2: {:x}", val2); // 5
    }
}
fn main() {
    let arr: [i32; 5] = [1, 2, 3, 4, 5];
    let address: &[i32; 5] = &arr;
    println!("Address of arr: {:p}", address);  // 0x7ffe95900c9c
    // 轉型為fat pointer
    raw_slice(address as &[i32]);
}
```

