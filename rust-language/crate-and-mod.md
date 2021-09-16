# crate與mod

## 簡介

Rust用了兩個概念來管理專案：一個是crate，一個是mod。

* crate簡單理解就是一個項目 \(project\)。crate是Rust中的獨立編譯單元。每個crate對應生成一個庫或者可執行檔（如.lib.dll.so.exe等）。
  * 官方有一個crate倉庫 https://crates.io/，可以供用戶發佈各種各樣的庫，用戶也可以直接使用這裡面的開源庫。
* mod簡單理解就是命名空間。mod可以嵌套，還可以控制內部元素的可見性。
* **crate和mod有一個重要區別是：crate之間不能出現迴圈引用；而mod是無所謂的，mod1要使用mod2的內容，同時mod2要使用mod1的內容，是完全沒問題的**。

在Rust裡面，crate才是一個完整的編譯單元（compile unit）。也就是說，rustc編譯器必須把整個crate的內容全部讀進去才能執行編譯，rustc不是基於單個的.rs檔或者mod來執行編譯的。作為對比，C/C++裡面的編譯單元是單獨的.c/.cpp檔以及它們所有的include檔。每個.c/.cpp檔都是單獨編譯，生成.o檔，再把這些.o檔連結起來。

Rust有一個極簡標準庫，叫作std，除了極少數嵌入式系統下無法使用標準庫之外，絕大部分情況下，我們都需要用到標準庫裡面的東西。為了效率，Rust編譯器對標準庫有特殊處理。**預設情況下，使用者不需要手動添加對標準庫的依賴，編譯器會自動引入對標準庫的依賴**。

## prelude模組

除此之外，標準庫中的某些type、trait、function、macro等實在是太常用了。每次都寫use語句確實非常無聊，因此標準庫提供了一個std：：prelude模組，在這個模組中匯出了一些最常見的類型、trait等東西，編譯器會為用戶寫的每個crate自動插入一句話：

```rust
use std::prelude::*;
```

這樣，標準庫裡面的這些最重要的類型、trait等名字就可以直接使用，而無須每次都寫全稱或者use語句。Prelude模組的程式碼在[rust/library/std/src/prelude/mod.rs](https://github.com/rust-lang/rust/blob/master/library/std/src/prelude/mod.rs)資料夾下。目前的mod.rs中，直接匯出了v1模組中的內容，而v1.rs中，則是編譯器為我們自動導入的相關trait和類型。

