# 複合資料類型

## tuple\(元組\)

tuple指的是“元組”類型，它通過圓括號包含一組運算式構成。tuple內的元素沒有名字。tuple是把幾個相同或相異類型組合到一起的最簡單的方式。

```rust
fn main() {
    // 元組中包含兩個元素,第一個是i32類型,第二個是bool類型
    let a = (1i32, false);
    println!("{:?}", a);
    // 元組中包含兩個元素,第二個元素本身也是元組,它又包含了兩個元素
    let b = ("a", (1i32, 2i32));
    println!("{:?}", b);

    // 如果元組中只包含一個元素，應該在後面添加一個逗號
    let c = (0,); // c是一個元組,它有一個元素
    let d = (0); // d是一個括弧運算式,它是i32類型
    println!("{:?}", c);
    println!("{}", d);

    // 訪問元組內部元素有兩種方法，
    // 一種是“模式匹配”（pattern destructuring），
    // 另外一種是“數字索引
    let p = (1i32, 2i32);
    let (a, b) = p;
    let x = p.0;
    let y = p.1;
    println!("{} {} {} {}", a, b, x, y);
}
```

### unit\(單元類型\)

元組內部也可以一個元素都沒有。這個類型單獨有一個名字，叫unit（單元類型）。

```rust
fn main() {
    // unit type
    let empty : () = ();
    
    // 空元組和空結構體struct Foo；一樣，都是佔用0記憶體空間。
    println!("size of i8 {}" , std::mem::size_of::<i8>());     // 1
    println!("size of char {}" , std::mem::size_of::<char>()); // 4
    println!("size of '()' {}" , std::mem::size_of::<()>());   // 0
}
```

在C++中，empty class佔用的是非零記憶體空間。

```cpp
class Empty {};
Empty emp;
assert(sizeof(emp) != 0);
```

## struct\(結構體\)

struct與tuple類似，也可以把多個類型組合到一起，作為新的類型。**區別在於，struct每個元素都有自己的名字**。

```cpp
// struct宣告
// 每個元素之間採用逗號分開，最後一個逗號可以省略不寫。
// 類型在冒號後面，但是不能使用自動類型推導功能，必須顯式指定。
struct Point {
    x: i32,
    y: i32,
}

fn main() {
    // struct類型的初始化語法類似於json的語法，
    // 使用“成員–冒號–值”的格
    let p = Point { x: 0, y: 0 };
    println!("Point is at {} {}", p.x, p.y);

    // 如果有區域變數名字和成員變數名字恰好一致，
    // 那麼可以省略掉重複的冒號初始化：
    // 剛好區域變數名字和結構體成員名字一致
    let x = 10;
    let y = 20;
    // 下麵是簡略寫法,等同於 Point { x: x, y: y },同名字的相對應
    let p = Point { x, y };
    println!("Point is at {} {}", p.x, p.y);

    // 訪問結構體內部的元素，也是使用“點”加變數名的方式。
    // 當然，也可以使用“模式匹配”功能
    // 聲明了px 和 py,分別綁定到成員 x 和成員 y
    let Point { x: px, y: py } = p;
    println!("Point is at {} {}", px, py);
    // 同理,在模式匹配的時候,如果新的變數名剛好和成員名字相同,可以使用簡寫方式
    let Point { x, y } = p;
    println!("Point is at {} {}", x, y);
}
```

Rust設計了一個語法糖，允許用一種簡化的語法賦值使用另外一個struct的部分成員。

```cpp
#[derive(Debug)]
struct Point3d {
    x: i32,
    y: i32,
    z: i32,
}
fn default() -> Point3d {
    Point3d { x: 0, y: 0, z: 0 }
}
fn main() {
    // 可以使用default()函數初始化其他的元素
    // ..expr 這樣的語法,只能放在初始設定式中,所有成員的最後最多只能有一個
    let origin = Point3d { x: 5, ..default() };
    let point = Point3d {
        z: 1,
        x: 2,
        ..origin
    };

    println!("{:?}, {:?}", origin, point);
}
```

struct內部成員也可以是空：

```cpp
//以下三種都可以,內部可以沒有成員
struct Foo1;
struct Foo2();
struct Foo3{}
```

