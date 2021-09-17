# 錯誤處理

## 主流模式：try-catch-finally

你學會了某種語言的try/catch，對這套機制的理解就能夠遷移到其他語言上了。除了C++沒有finally關鍵字外，像C\#、Python、Java都有基本一致的異常處理邏輯：

* try塊包住可能會出現的異常；
* 用catch將之捕獲；
* finally塊統一處理資源的清理；
* 對於自定義的函數，我們可以throw異常。

在這種異常處理系統中，對異常的定義是比較寬泛的：意料之外，情理之中。正是“異常”在語義上的模糊性，才產生了很多最佳實踐來指導異常的使用。從“正常到異常的程度”上，大致上可以歸為4類：

### 0 正常：不要用異常來進行流程控制，異常只用來處理“意外”。

這條教導告訴我們，如果分不清“異常”，那麼至少在“正常”的、沒有意外的流程裡，絕對不要用“異常機制來代替”。否則，代碼可讀性、可維護性將是災難。

```java
// Java
​
try{
​
}catch(FileNotFoundException f){
​
}catch(IOException i){
​
}finally{
​
}
```

### 1 人造語義異常

如果主流程中存在一個連續的“闖關”pipeline（一組按順序的調用，成功執行才能執行下一個，否則都算失敗），那麼可以使用try塊來集中放置主流程代碼，catch塊來集中處理失敗情況，避免if-else箭頭形代碼。

```cpp
try {
    getSomeThing_1();
    getSomeThing_2();
    getSomeThing_3();
catch(Exception e) {
    // deal with it
}
```

### 2 情理中的意外，可恢復

前面提到的非法字元、找不到檔案、連接不上，基本是公認的“意外”情況，基本都使用拋出異常的方式，但是這種情況，通常都會進行捕獲，並進行恢復。

### 3 無法意料的致命意外，不可恢復。

通常這種情況是，程式自身已經沒有修復的空間，程式會中止：

* Bug：邏輯錯誤導致的溢位、除0；
* 致命錯誤：比如os產生的Error；

## Rust的Panic！

Rust裡沒有異常。

### 0 正常，以返回值的形式。



### 1 致命錯誤，不可恢復，非崩不可。

* 一旦存在不可恢復的錯誤，Rust使用Panic！巨集來終止程式（執行緒）。一旦Panic！巨集出手，基本沒得救（panic::catch\_unwind是個例外，稍後說）。執行時預設會進行stack unwind（棧反解），一層層上去，直到執行緒的頂端。
* 有些情況Panic！是你的程式所依賴的庫產生的，比如陣列越界存取時的實現。
* 另一種情況，是你自己的程式邏輯判斷產生了不可恢復的錯誤，可以手動觸發Panic！巨集來終止程式。Panic！的使用與throw很類似。

##  Rust的返回值Result

對於可恢復的錯誤，Rust一律使用返回值來進行檢查，而且提倡採用內建列舉Result，還在實踐層面給了一定的約束：對於返回值為Result型別的函式，呼叫方如果沒有進行接收，編譯期會產生警告。很多庫函式都通過Result來告知呼叫方執行結果，讓呼叫方來決定是否嚴重到了使用Panic！的程度。

```cpp
enum Result<T, E>{
    Ok(T),
    Err(E),
}
```

在Rust標準庫中，可以找到許多以Result命名的型別，它們通常是Result泛型的特定版本，比如File::open的返回值就是把T替換成了std::fs::File，把E替換成了std::io::Error。

### panic::catch\_unwind

```cpp
use std::panic;
​
let result = panic::catch_unwind(|| {
    println!("hello!");
});
assert!(result.is_ok());
​
let result = panic::catch_unwind(|| {
    panic!("oh no!");
});
assert!(result.is_err());
```

沒錯，它的行為幾乎就是try/catch了：panic！巨集被捕獲了，程式並也沒有掛，返回了Err。儘管如此，Rust的目的並不是讓它成為try/catch機制的實現，而是當Rust和其他程式語言互動時，避免其他語言程式碼塊throw出異常。所以呢，錯誤處理的正道還是用Result。

從catch\_unwind的名字上，需要留意下unwind這個限定詞，它意味著只有預設進行棧反解的panic可以被捕獲到，如果是設為直接終止程式的panic，就逮不住了。




