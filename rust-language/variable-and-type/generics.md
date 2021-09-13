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

