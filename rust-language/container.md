# 容器

## 常用的容器

| 名稱 | 描述 |
| :--- | :--- |
| Vec | 可變長度陣列，連續儲存資料 |
| VecDeque | 雙向佇列，適用於從頭部與尾部插入與刪除資料 |
| LinkedList | 雙向鏈結，非連續儲存資料 |
| HashMap | 以hash算法儲存key-value對 |
| BTreeMap | 以Btree算法儲存key-value對 |
| HashSet | 基於Hash算法的集合，相當於沒有值的HashMap |
| BTreeSet | 基於BTree的集合 相當於沒有值的BTreeMap |
| BinaryHeap | 基於二元樹堆積實現的優先佇列 |

## 迭代器 \(iterator\)

Rust的迭代器是指實現了Iterator trait的類型。

```rust
trait Iterator {
    type Item;
    fn next(&mut self) -> Option<Self::Item>;
    // ...
}
```

它最主要的一個方法就是`next()`，返回一個`Option<Item>`。一般情況返回`Some(Item)`；如果迭代運算完成，就返回`None`。

```rust
use std::iter::Iterator;
// 自訂的類別
struct Seq {
    current: i32,
}
impl Seq {
    fn new() -> Self {
        Seq { current: 0 }
    }
}
// 實現迭代器
impl Iterator for Seq {
    type Item = i32;
    fn next(&mut self) -> Option<i32> {
        if self.current < 100 {
            self.current += 1;
            return Some(self.current);
        } else {
            return None;
        }
    }
}
fn main() {
    let mut seq = Seq::new();
    while let Some(i) = seq.next() {
        println!("{}", i);
    }
}
```

## 迭代器的組合

Rust標準庫有一個命名規範，從容器創造出迭代器一般有三種方法

* `iter()`創造一個Item是`&T`類型的迭代器；
* `iter_mut()`創造一個Item是`&mut T`類型的迭代器；
* `into_iter()`創造一個Item是`T`類型的迭代器。

```rust
fn main() {
    let v = vec![1, 2, 3, 4, 5];
    // Item為&T的iterator
    let mut iter = v.iter();
    while let Some(i) = iter.next() {
        println!("{}", i);
    }
}
```

如果迭代器就是這麼簡單，那麼它的用處基本就不大了。Rust的迭代器有一個重要特點，那它就是可組合的（composability）。

Iterator trait裡面還有一大堆的方法，比如nth、map、filter、skip\_while、take等等，這些方法都有預設實現，它們可以統稱為adapters（適配器）。它們有個共性，返回的是一個具體類型，而這個類型本身也實現了Iterator trait。這意味著，我們調用這些方法可以從一個迭代器創造出一個新的迭代器。

```rust
// 迭代器寫法，容易平行化
fn main() {
    let v = vec![1, 2, 3, 4, 5, 6, 7, 8, 9];
    let mut iter = v
        .iter()
        .take(5)
        .filter(|&x| x % 2 == 0)
        .map(|&x| x * x)
        .enumerate();
    while let Some((i, v)) = iter.next() {
        println!("{} {}", i, v);
    }
}

// 傳統等價寫法
fn main() {
    let v = vec![1, 2, 3, 4, 5, 6, 7, 8, 9];
    let mut iter = v.iter();
    let mut count = 0;
    let mut index = 0;
    while let Some(i) = iter.next() {
        if count < 5 {
            count += 1;
            if (*i) % 2 == 0 {
                let s = (*i) * (*i);
                println!("{} {}", index, s);
                index += 1;
            }
        } else {
            break;
        }
    }
}
```

。兩個版本相比較，迭代器的可讀性是不言而喻的。這種抽象相比于直接在傳統的迴圈內部寫各種邏輯是有優勢的，特別是如果我們想把迭代器改成並存執行是非常容易的事情。而傳統的寫法涉及細節太多，不太容易改成並存執行。

分析迭代器的實現原理我們也可以知道：**構造一個迭代器本身，是代價很小的行為，因為它只是初始化了一個物件，並不真正產生或消費資料。**不論迭代器內部嵌套了多少層，最終消費資料還是要通過調用next（）方法實現的。這個特點，也被稱為惰性求值（lazy evaluation）。

```rust
fn main() {
    let v = vec![1, 2, 3, 4, 5];
    // 什麼事都沒做，因為map只是iterator
    // 沒有真正操作內容資料
    let data = v.iter().map( |x| println!("{}", x));
    // 要真正走訪iterator才會執行
    for _ in data{
    }
}
```

## for循環

Rust裡面更簡潔、更自然地使用迭代器的方式是使用for迴圈。本質上來說，for迴圈就是專門為迭代器設計的一個語法糖。for迴圈可以對針對陣列切片、字串、Range、Vec、LinkedList、HashMap、BTreeMap等所有具有迭代器的類型執行迴圈，而且還允許我們針對自訂類型實現迴圈。

```rust
use std::collections::HashMap;
fn main() {
    let v = vec![1, 2, 3, 4, 5, 6, 7, 8, 9];
    for i in v {
        println!("{}", i);
    }
    let map: HashMap<i32, char> = [(1, 'a'), (2, 'b'), (3, 'c')].iter().cloned().collect();
    for (k, v) in &map {
        println!("{} : {}", k, v);
    }
}
```



