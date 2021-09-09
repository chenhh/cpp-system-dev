# if let

Rust不僅能在match運算式中執行“模式解構”，在let語句中，也可以應用同樣的模式。Rust還提供了if-let語法糖。它的語法為`if let PATTERN=EXPRESSION{BODY}`。後面可以跟一個可選的else分支。

```rust
// 原始寫法，從Some中取出x傳入函數
match optVal {
    Some(x) => {
        doSomethingWith(x);
    }
    _ => {}
}

// 簡化寫法一
// 首先判斷它一定是 Some(_)
if optVal.is_some() { 
    // 然後取出內部的資料
    let x = optVal.unwrap(); 
    doSomethingWith(x);
}

// 簡化寫法二，使用if let
if let Some(x) = optVal {
    doSomethingWith(x);
}
```

