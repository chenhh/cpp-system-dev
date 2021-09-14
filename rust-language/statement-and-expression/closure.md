# 閉包\(closure\)

## 簡介

closure看起來和普通函數很相似，但實際上它們有許多區別。

* **最主要的區別是，closure可以“捕獲”外部環境變數，而fn不可以，fn要使用外部變量只能由參數傳入，且要考慮是move, copy還是borrow語意**。對於不需要捕獲環境變數的場景，普通函數fn和closure是可以互換使用的。
* fn定義和調用的位置並不重要，Rust中是不需要前向聲明的。只要函式定義在當前範圍內是可以觀察到的，就可以直接調用，不管在源碼內的相對位置如何**。相對而言，closure更像是一個的變數，它具有和變數同樣的“生命週期”**。

閉包（closure）是一種匿名函數，具有“捕獲”外部變數的能力。閉包有時候也被稱作lambda運算式\(C++\)。它有兩個特點：

1. 可以像函數一樣被調用；
2. 可以捕獲當前環境中的變數。

```rust
fn main() {
    // 以上閉包有兩個參數，以兩個|包圍。執行語句包含在{}中。
    // 閉包的參數和返回數值型別的指定與普通函數的語法相同。
    let add = |a: i32, b: i32| -> i32 {
        return a + b;
    };
    let x = add(1, 2);
    println!("result is {}", x);
    
    // 閉包的參數和返回數值型別都是可以省略的
    let add2 = |a, b| { a + b };
    let x2 = add2(2,3);
    println!("result2 is {}", x2);
    
    // 如果閉包的語句體只包含一條語句，
    // 那麼外層的大括弧也可以省略；如果有多條語句則不能省略。
    let add3 = |a, b|  a + b ;
    let x3 = add3(3,4);
    println!("result3 is {}", x3);
}
```

```rust
fn main() {
    let x = 1_i32;
    // closure可以訪問同區間的變數，
    // 而一般的fn做不到
    let inner_add = || x + 1;
    let x2 = inner_add();
    println!("result is {}", x2);
}
```

