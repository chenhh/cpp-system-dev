# 靜態與動態分派

## 簡介

閉包中有簡單介紹[靜態與動態分派](closure.md#jing-tai-yu-dong-tai-fen-pai)。

* 所謂“靜態分派”，是指具體調用哪個函數，在編譯階段就確定下來了。**Rust中的“靜態分派”靠泛型以及impl trait來完成**。對於不同的泛型類型參數，編譯器會生成不同版本的函數，在編譯階段就確定好了應該調用哪個函數。
* 所謂“動態分派”，是指具體調用哪個函數，在執行階段才能確定。**Rust中的“動態分派”靠Trait Object來完成**。Trait Object本質上是指標，它可以指向不同的類型；指向的具體類型不同，調用的方法也就不同。

```rust
trait Bird {
    fn fly(&self);
}
struct Duck;
struct Swan;
// 兩個類別都實現Bird trait
impl Bird for Duck {
    fn fly(&self) {
        println!("duck duck");
    }
}
impl Bird for Swan {
    fn fly(&self) {
        println!("swan swan");
    }
}
// 靜態分派
fn test_static<T: Bird>(arg: T) {
    arg.fly();
}
// 動態分派, 用Box將變數裝箱
fn test_dynamic(arg: Box<dyn Bird>) {
    arg.fly();
}

fn main() {
    let d = Duck;
    let s = Swan;
    test_static(d);
    test_static(s);

    let db = Box::new(Duck);
    let ds = Box::new(Swan);
    test_dynamic(db);
    test_dynamic(ds);
}
```

上例中，test\_dynamic函數的參數既可以是Box&lt;Duck&gt;類型，也可以是Box&lt;Swan&gt;類型，一樣實現了“多態”。但在參數類型這裡已經將“具體類型”資訊抹掉了，我們只知道它可以調用Bird trait的方法。而具體調用的是哪個版本的方法，實際上是由這個指標的值來決定的。這就是“動態分派”。

