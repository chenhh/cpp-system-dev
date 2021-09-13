# panic

## 簡介

在Rust中，有一類錯誤叫作panic。

```rust
fn main() {
    let x: Option<i32> = None;
    // 無法由None取出資料, 產生panic
    x.unwrap();
}
```

在Rust中，正常的錯誤處理應該儘量使用Result類型。Panic則是作為一種“fail fast”機制，處理那種萬不得已的情況。

比如，上面例子中的unwrap方法，試圖把Option&lt;i32&gt;轉換為i32類型，當參數是None的時候，這個轉換是沒辦法做到的，這種時候就只能使用panic。所以，一般情況下，使用者應該使用unwrap\_or等不會製造panic的方法。

## panic實現機制

在Rust中，Panic的實現機制有兩種方式：unwind和abort，依作業系統或是使用者自行選擇而定。

* unwind方式在發生panic的時候，會一層一層地退出函式呼叫堆疊，在此過程中，當前堆疊內的區域變數還可以正常解構。
* abort方式在發生panic的時候，會直接退出整個程式。

**在常見的作業系統上，預設情況下，編譯器使用的是unwind方式**。所以在發生panic的時候，我們可以通過一層層地調用堆疊找到發生panic的第一現場。

但是，unwind並不是在所有平臺上都能獲得良好支持的。在某些嵌入式系統上，unwind根本無法實現，或者佔用的資源太多。在這種時候，我們可以選擇使用abort方式實現panic。

編譯器提供了一個選項，供使用者指定panic的實現方式：

```bash
rustc -C panic=unwind test.rs
rustc -C panic=abort test.rs
```

panic中有catch\_unwind機制絕對不是設計用於模擬“try-catch”機制的。請大家永遠不要利用這個機制來做正常的流程控制。R**ust推薦的錯誤處理機制是用返回值**。

## panic使用時機

**panic出現的場景一般是：如果繼續執行下去就會有極其嚴重的記憶體安全問題，這種時候讓程式繼續執行導致的危害比崩潰更嚴重**，此時panic就是最後的一種錯誤處理機制。

它的主要用處參考下面的情況：

* 在FFI場景下的時候，當C語言調用了Rust的函數，在Rust內部出現了panic，如果這個panic在Rust內部沒有處理好，直接扔到C程式碼中去，會導致C語言產生“未定義行為”（undefined behavior）。
* 某些高級抽象機制需要阻止堆疊展開，比如執行緒池。如果一個執行緒中出現了panic，我們希望只把這個執行緒關閉，而不至於將整個執行緒池“拖下水”。

## panic safety

