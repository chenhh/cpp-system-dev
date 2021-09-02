# crate與mod

Rust的程式碼從邏輯上是分crate和mod管理的。所謂crate可以理解為“project”。

每個crate是一個完整的編譯單元，它可以生成為一個lib或者exe可執行檔。而在crate內部，則是由mod這個概念管理的，可以理解為namespace。我們可以使用use語句把其他模組中的內容引入到當前模組中來。

Rust有一個極簡標準庫，叫作std，除了極少數嵌入式系統下無法使用標準庫之外，絕大部分情況下，我們都需要用到標準庫裡面的東西。為了效率，Rust編譯器對標準庫有特殊處理。**預設情況下，使用者不需要手動添加對標準庫的依賴，編譯器會自動引入對標準庫的依賴**。

## prelude模組

除此之外，標準庫中的某些type、trait、function、macro等實在是太常用了。每次都寫use語句確實非常無聊，因此標準庫提供了一個std：：prelude模組，在這個模組中匯出了一些最常見的類型、trait等東西，編譯器會為用戶寫的每個crate自動插入一句話：

```rust
use std::prelude::*;
```

這樣，標準庫裡面的這些最重要的類型、trait等名字就可以直接使用，而無須每次都寫全稱或者use語句。Prelude模組的程式碼在[rust/library/std/src/prelude/mod.rs](https://github.com/rust-lang/rust/blob/master/library/std/src/prelude/mod.rs)資料夾下。目前的mod.rs中，直接匯出了v1模組中的內容，而v1.rs中，則是編譯器為我們自動導入的相關trait和類型。

