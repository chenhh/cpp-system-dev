# unsafe區塊

## 簡介

有一些情況，編譯器的靜態檢查是不夠用的，它沒辦法自動推理出來這段程式碼究竟是不是安全的。這種時候，我們就需要使用unsafe關鍵字來保證程式碼的安全性。

Rust的unsafe關鍵字有以下幾種用法：

* 用於修飾函數fn；
* 用於修飾程式碼塊；
* 用於修飾trait；
* 用於修飾impl。

當一個fn是unsafe的時候，意味著我們在調用這個函數的時候需要非常小心。它可能要求調用者滿足一些其他的重要約束，而這些約束條件無法由編譯器自動檢查來保證。有unsafe修飾的函數，要麼使用unsafe語句塊調用，要麼在unsafe函數中調用。

**因此需要注意，unsafe函數是具有“傳遞性”的，unsafe函數的“調用者”也必須用unsafe修飾**。

```rust
// 函數簽名
pub unsafe fn from_raw_parts(buf: *mut u8, length: usize, capacity: usize) ->
String
// 自己保證這個緩衝區包含的是合法的 utf-8 字串
let s = unsafe { String::from_raw_parts(ptr as *mut _, len, capacity) } ;
```

使用unsafe關鍵字包圍起來的語句塊，裡面可以做一些一般情況下做不了的事情。但是，它也是有規矩的。與普通程式碼比起來，它多了以下幾項能力：

* 對裸指標執行解引用操作；
* 讀寫可變靜態變數；
* 讀union或者寫union的非Copy成員；
* 調用unsafe函數

在Rust中，有些地方必須使用unsafe才能實現。比如標準庫提供的一系列intrinsic函數，很多都是unsafe的，再比如調用extern函數必須在unsafe中實現。另外，一些重要的資料結構內部也使用了unsafe來實現一些功能。

**當unsafe修飾一個trait的時候，那麼意味著實現這個trait也需要使用unsafe。**比如在後面講執行緒安全的時候會著重講解的Send、Sync這兩個trait。因為它們很重要，是實現執行緒安全的根基，如果由程式師來告訴編譯器，強制指定一個類型是否滿足Send、Sync，那麼程式師自己必須很謹慎，必須很清楚地理解這兩個trait代表的含義，編譯器是沒有能力推理驗證這個impl是否正確的。這種impl對程式的安全性影響很大。

