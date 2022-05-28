# Option

## 簡介

Option是一種枚舉類型，主要包括兩種值：`Some(T)`和`None`，Rust也是靠它來避免空指標異常的。

## 取值的方法

一般用兩種方法取值，一種是expect，另一種是unwrap系列的方法。

```rust
fn main() {
    let a = Some("a");
    let b: Option<&str> = None;
    assert_eq!(a.expect("a is none"), "a");
    //匹配到None會引起panic，列印的錯誤是expect的參數資訊
    // assert_eq!(b.expect("b is none"), "b is none");

    assert_eq!(a.unwrap(), "a"); //如果a是None，則會引起panic
    assert_eq!(b.unwrap_or("b"), "b"); //匹配到None時返回指定值
    let k = 10;
    // 與unwrap_or類似，只不過參數是FnOnce() -> T
    assert_eq!(Some(4).unwrap_or_else(|| 2 * k), 4);
    assert_eq!(None.unwrap_or_else(|| 2 * k), 20);
}
```

有時我們會覺得每次處理Option都需要先提取，然後再做相應計算這樣的操作比較麻煩。可用map和and\_then這兩種方法。

其中map方法和unwrap一樣，也是一系列方法，包括map、map\_or和map\_or\_else。map會執行參數中閉包的規則，然後將結果再封為Option並返回。

```rust
fn main() {
    let some_str = Some("Hello!");
    let some_str_len = some_str.map(|s| s.len());
    assert_eq!(some_str_len, Some(6));
}
```

但是，如果參數本身返回的結果就是Option的話，處理起來就比較麻煩，因為每執行一次map都會多封裝一層，最後的結果有可能是Some(Some(Some(...)))這樣N多層Some的巢狀。這時，我們就可以用and\_then來處理了。

```rust
fn main() {
    assert_eq!(Some(2).and_then(sq).and_then(sq), Some(16));
}

fn sq(x: u32) -> Option<u32> { 
    Some(x * x) 
}
```
