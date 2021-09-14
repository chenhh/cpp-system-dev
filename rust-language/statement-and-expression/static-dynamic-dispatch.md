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

## trait object

指向trait的指標就是trait object。假如Bird是一個trait的名稱，那麼dyn Bird就是一個DST動態大小類型。

&dyn Bird、&mut dyn Bird、Box&lt;dyn Bird&gt;、\*const dyn Bird、\*mut dyn Bird以及Rc&lt;dyn Bird&gt;等等都是Trait Object。

trait object這個名字以後也會被改為dynamic trait type。impl Trait forTrait這樣的語法同樣會被改為impl Trait for dyn Trait。這樣能更好地跟impl Trait語法對應起來。

當指標指向trait的時候，這個指標就不是一個普通的指標了，變成一個“胖指標”。前文所講解的DST類型：

* 陣列類型\[T\]是一個DST類型，因為它的大小在編譯階段是不確定的。
* 相對應的，&\[T\]類型就是一個“胖指標”，它不僅包含了指標指向陣列的其中一個元素，同時包含一個長度資訊。它的內部表示實質上是Slice類型。

同理，Bird只是一個trait的名字，符合這個trait的具體類型可能有多種，這些類型並不具備同樣的大小，因此使用dyn Bird來表示滿足Bird約束的DST類型。指向DST的指標理所當然也應該是一個“胖指標”，它的名字就叫trait object。

比如`Box<dyn Bird>`，它的內部表示可以理解成下面這樣：

```rust
pub struct TraitObject {
    pub data: *mut (),
    pub vtable: *mut (),
}
```

