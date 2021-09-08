# 模式解構\(pattern destructure\)

## 簡介

Rust中模式解構功能設計得非常美觀，它的原則是：構造和解構遵循類似的語法，我們怎麼把一個資料結構組合起來的，我們就怎麼把它拆解開來。

注意這裡的“Destructure”和“Destructor”是完全不同的兩個單詞，代表完全不同的含義。

* “Destructure”的意思是把原來的結構肢解為單獨的、局部的、原始的部分；
* “Destructor”是指“解構器”，是一個與“構造器”相對應的概念，是在物件被銷毀的時候調用的。

```rust
let tuple = (1_i32, false, 3f32);    // 建構tuple
let (head, center, tail) = tuple;    // 解構tuple至各變數
```

```rust
/* 我們首先構造了一個T2類型的變數x，
 * 它內部又嵌套包含了其他的結構體。
 * 實際上，我們完全可以一次性解構多個層次，
 * 直接把這個物件內部深處的元素拆解出來
*/
// struct tuple
struct T1(i32, char);
// struct
struct T2 {
    item1: T1,
    item2: bool,
}
fn main() {
    // 建構T2 structure
    let x = T2 {
        item1: T1(0, 'A'),
        item2: false,
    };
    // 解構x至T2 struct對應的變數中
    let T2 {
        item1: T1(value1, value2),
        item2: value3,
    } = x;
    println!("{} {} {}", value1, value2, value3);
}
```

Rust的“模式解構”功能不僅出現在let語句中，還可以出現在match、if let、while let、函式呼叫、閉包調用等情景中。而match具有功能最強大的模式匹配。

## match

```rust
enum Direction {
    East,
    West,
    South,
    North,
}
fn print(x: Direction) {
    // 因此match列舉了所有enum的狀態，
    // 所以不必加上_的例外處理
    match x {
        Direction::East => {
            println!("East");
        }
        Direction::West => {
            println!("West");
        }
        Direction::South => {
            println!("South");
        }
        Direction::North => {
            println!("North");
        }
    }
}
fn main() {
    let x = Direction::East;
    print(x);
}
```

Rust要求match需要對所有情況做完整的、無遺漏的匹配，如果漏掉了某些情況，是不能編譯通過的。exhaustive意思是無遺漏的、窮盡的、徹底的、全面的。exhaustive是Rust模式匹配的重要特點。

```rust
fn print(x: Direction) {
    match x {
        Direction::East => {
            println!("East");
        }
        Direction::West => {
            println!("West");
        }
        Direction::South => {
            println!("South");
        }
        // 因此沒有列出Direction::North，
        // 可用_捕抓未列出的所有狀態
        _ => {
            println!("Other");
        }
    }
}
```

正因為如此，在多個專案之間有依賴關係的時候，在上游的一個庫中對enum增加成員，是一個破壞相容性的改動。因為增加成員後，很可能會導致下游的使用者match語句編譯不過。為解決這個問題，Rust提供了一個叫作non\_exhaustive的功能（目前還沒有穩定）。

```rust
#[non_exhaustive]
pub enum Error {
    NotFound,
    PermissionDenied,
    ConnectionRefused,
}
```

上游庫作者可以用一個叫作“non\_exhaustive”的attribute來標記一個enum或者struct，這樣在另外一個項目中使用這個類型的時候，無論如何都沒辦法在match運算式中通過列舉所有的成員實現完整匹配，必須使用底線才能完成編譯。這樣，以後上游庫裡面為這個類型添加新成員的時候，就不會導致下游專案中的編譯錯誤了因為它已經存在一個預設分支匹配其他情況。

## 底線\(underline\)

除了在match中表示未捕捉到的所有狀態之外，**底線還能用在模式匹配的各種地方，用來表示一個預留位置，雖然匹配到了但是忽略它的值的情況**：

```rust
struct P(f32, f32, f32);
fn calc(arg: P) -> f32 {
    // 模式解構, 匹配 tuple struct,
    // 但是忽略第二個成員的值
    let P(x, _, y) = arg;
    x * x + y * y
}
fn main() {
    let t = P(1.0, 2.0, 3.0);
    println!("{}", calc(t));  // 10
}
```

上例可在參數傳入函數時，直接解構且忽略變數：

```rust
struct P(f32, f32, f32);
// 參數類型是 P,參數本身是一個模式,解構之後,變數x、y分別綁定了第一個和第三個成員
fn calc(P(x, _, y): P) -> f32 {
    x * x + y * y
}
fn main() {
    let t = P(1.0, 2.0, 3.0);
    println!("{}", calc(t)); // 10
}
```

