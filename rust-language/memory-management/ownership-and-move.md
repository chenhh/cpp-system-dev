# 所有權與移動\(ownership and move\)

## 所有權\(ownership\)

“所有權”代表著以下意義：

* 每個值在Rust中都有一個變數來管理它，這個變數就是這個值、這塊記憶體的所有者；
* 每個值在一個時間點上只有一個管理者；
* 當變數所在的作用域結束的時候，變數以及它代表的值將會被銷毀。

```rust
fn main() {
    let mut s = String::from("hello");
    // 因為對s有寫入的操作，因此必須宣告為mut S
    s.push_str(" world");
    println!("{}", s);
}
```

* 當我們聲明一個變數s，並用String類型對它進行初始化的時候，這個變數s就成了這個字串的“所有者”。
* 如果我們希望修改這個變數，可以使用mut修飾s，然後調用String類型的成員方法來實現。
* 當main函數結束的時候，s將會被解構，它管理的記憶體（不論是堆積上的，還是堆疊上的）則會被釋放。
* 我們一般把變數從出生到死亡的整個階段，叫作一個變數的“生命週期”（lifetime）。比如這個例子中的區域變數s，它的生命週期就是從let語句開始，到main函數結束。

```rust
fn main() {
    let s = String::from("hello");
    let s1 = s;
    // error, borrow of moved value: `s`
    // 因為s對String的所有權已經轉移給s1，
    // s的生命週期結束, 不可再被使用
    // 解法：改用borrow，let s1 = &s;
    // 解法：改用clone, let s1 = s.clone();
    println!("{}", s);
}
```

在Rust裡面，不可以做“設定運算子重載”，若需要“深複製” \(deep copy\)（指的是對於複雜結構中所有元素的複製，而不是單純以共享指標指向該結構），必須手工調用clone方法。這個clone方法來自於std:clone::Clone這個trait。clone方法裡面的行為是可以自訂的。

## 移動語意\(move\)

一個變數可以把它擁有的值轉移給另外一個變數，稱為“所有權轉移”。設定陳述式、函式呼叫、函數返回等，都有可能導致所有權轉移。

```rust
fn create() -> String {
    let s = String::from("hello");
    return s; // 所有權轉移,從函數內部移動到外部
}
fn consume(s: String) {
    // 所有權轉移,從函數外部移動到內部
    println!("{}", s);
}
fn main() {
    let s = create();
    consume(s);
}
```

