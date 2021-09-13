# 泛型\(generics\)

## 簡介

泛型（Generics）是指把類型抽象成一種“參數”，資料和演算法都針對這種抽象的類型參數來實現，而不針對具體類型。當我們需要真正使用的時候，再具體化、產生實體類型參數。

## 資料結構中的泛型

```rust
enum Option<T> {
    Some(T),
    None,
}

let x: Option<i32> = Some(42);
let y: Option<f64> = None;
```

這裡的&lt;T&gt;實際上是聲明了一個“類型”參數。在這個Option內部，Some（T）是一個tuple struct，包含一個元素類型為T。這個泛型參數類型T，可以在使用時指定具體類型。

泛型參數可以有多個也可以有預設值。

```rust
struct S<T = i32> {
    data: T,
}
fn main() {
    // 使用預設的type
    let v1 = S { data: 0 };
    // 指定T實現的type
    let v2 = S::<bool> { data: false };
    // 0 false
    println!("{} {}", v1.data, v2.data);
}
```

使用不同類型參數將泛型類型具體化後，獲得的是完全不同的具體類型。如Option&lt;i32&gt;和Option&lt;i64&gt;是完全不同的類型，不可通用，也不可相互轉換。當編譯器生成程式碼的時候，它會為每一個不同的泛型參數生成不同的程式碼。各種自訂複合類型都可以攜帶任意的泛型參數。

Rust規定，所有的泛型參數必須是真的被使用過的，否則會產生編譯錯誤。

```rust
// compile error, tpye T沒有被使用過
struct Num<T> {
    data: i32,
}
```

## 函數中的泛型

```rust
fn compare_option<T>(first: Option<T>, second: Option<T>) -> bool {
    match (first, second) {
        (Some(..), Some(..)) => true,
        (None, None) => true,
        _ => false,
    }
}
```

函數compare\_option有一個泛型參數T，兩個形參類型均為Option&lt;T&gt;。這意味著這兩個參數必須是完全一致的類型。如果我們在參數中傳入兩個不同的Option，會導致編譯錯誤。

```rust
fn main() {
    // 類型不匹配編譯錯誤
    println!("{}", compare_option(Some(1i32), Some(1.0f32)));
}
```

編譯器在看到這個函式呼叫的時候，會進行類型檢查：

* first的形參類型是Option&lt;T&gt;、實參類型是Option&lt;i32&gt;，
* second的形參類型是Option&lt;T&gt;、實參類型是Option&lt;f32&gt;。

這時編譯器的類型推導功能會進行一個類似解方程組的操作：由Option&lt;T&gt;==Option&lt;i32&gt;可得T==i32，而由Option&lt;T&gt;==Option&lt;f32&gt;又可得T==f32。這兩個結論產生了矛盾，因此該方程組無解，出現編譯錯誤。

如果我們希望參數可以接受兩個不同的類型，那麼需要使用兩個泛型參數：

```rust
fn compare_option<T1, T2>(
    first: Option<T1>, 
    second: Option<T2>) -> bool { ... }
```

一般情況下，調用泛型函數可以不指定泛型參數類型，編譯器可以通過類型推導自動判斷。某些時候，如果確實需要手動指定泛型參數類型，則需要使用function\_name：：&lt;type params&gt;（function params）的語法：

```rust
compare_option::<i32, f32>(Some(1), Some(1.0));
```

**Rust沒有C++那種無限制的ad hoc式的函數重載功能**。現在沒有，將來也不會有。主要原因是，這種隨意的函數重載對於程式碼的維護和可讀性是一種傷害。

通過泛型來實現類似的功能是更好的選擇。如果說，不同的參數類型，沒有辦法用trait統一起來，利用一個函數體來統一實現功能，那麼它們就沒必要共用同一個函數名。它們的區別已經足夠大，所以理應使用不同的名字。強行使用同一個函數名來表示區別非常大的不同函數邏輯，並不是好的設計。

我們還有另外一種方案，可以把不同的類型統一起來，那就是enum。**通過enum的不同成員來攜帶不同的類型資訊，也可以做到類似“函數重載”的功能。但這種做法跟“函數重載”有本質區別，因為它是有執行時開銷的。**

enum會在執行階段判斷當前成員是哪個變體，而“函數重載”以及泛型函數都是在編譯階段靜態分派的。同樣，Rust也不鼓勵大家僅僅為了省去命名的麻煩，而強行把不同類型用enum統一起來用一個函數來實現。如果一定要這麼做，那麼最好是有一個好的理由，而不僅是因為懶得給函數命名而已。

