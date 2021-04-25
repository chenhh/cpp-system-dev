# 記憶體保護機制

## 簡介

由保護模式的名稱，就可以看出保護機制的重要性。保護機制可以減少系統當機，加強作業系統的穩定性，還可以幫忙找出程式的問題。

IA-32 的保護機制，基本上是架構在**「特權等級」上（在 segment 有四個等級，而在 page 有兩個等級）**。因此，作業系統可以把重要的核心、系統程式、服務等等部分，放在具較高的特權等級的 segment 中；而把一般的應用程式放在特權等級最低的 segment 中。**處理器會阻止等級較低的 segment 任意對等級較高的 segment 進行存取**。這樣一來，就可以避免一個不正常的應用程式，破壞了整個系統。

在存取一記憶體位址之前，就會開始進行檢查。如果有任何違反保護規則的情形，就會發出例外（exception）。保護的規則包括：邊界檢查、型態檢查、特權等級檢查、可定址空間限制、程序進入點限制、和指令集限制。這些檢查和位址轉換是同時進行的，所以對執行效能不會有影響。

**要進入保謢模式，只要把 CR0 的 PE（protection enable，第 0 bit）設為 1 就可以開啟 segment 保護。**在保護模式中，並沒有什麼方法可以把保護機制暫時開啟或關閉。如果不想使用任何保護功能，可以把所有的 segment 的特權等級都設定為 0（最高的等級），這樣會取消 segment 和 segment 之間的檢查動作。不過，邊界檢查和型態檢查還是會有作用。

**分頁的保謢，也是在開啟分頁功能之後，就會自動作用**。同樣的，也沒有特別的方法可以暫時關閉或開啟分頁的保護機制。不過，就像 segment 一樣，可以把 CR0 的 WP（write protect，第 16 bit）設為 0，再把所有的分頁的 U/S 和 R/W 旗標都設為 1（參考「記憶體管理」的「分頁機制」）。這樣會把所有的分頁設定成可寫入、且在 user level 中，如此一來，分頁的保護機制就等於沒有作用了。

## 特權等級

在 segment 的層次中，有四種特權等級，由 0 到 3。

* 第 0 級（又稱 ring 0）是權力最大的，而第 3 級（又稱 ring 3）則是權力最小的。一般的情形中，應用程式是在 ring 3 中執行，而系統核心則在 ring 0 中執行。
* 大部分的作業系統只用到兩個等級，即 ring 0 和 ring 3（只用到兩個等級的作業系統，應該要使用 ring 0 和 ring 3）。
* 不過，如果有特別需要，也可以使用權力介於 ring 0 和 ring 3 之間的 ring 1 和 ring 2。例如，在微核心（micro kernel）系統中，微核心在 ring 0 中執行，而系統服務則可以在 ring 1（或 ring 2）中執行。

**在一般的情形下，權力較低的程式不能存取權力較高的程式或資料。事實上，即使是權力較高的程式，也不能直接執行權力較低的程式，而必須經由 call gate 來呼叫。**當然，也是有例外的情形。

特權等級有三種：

* **目前特權等級（Current Privilege Level, CPL）：目前執行的程式或工作的特權等級，存放在 CS 和 SS 的第 0 和第 1 位元（分段架構）**。一般情形下，CPL 會和目前程式所在的 segment 的特權等級（即 DPL）相同。而在轉移控制權時（例如，用 JMP 或 CALL 命令跳到另一 segment 中），CPL 會改變為新的 segment 的 DPL。不過，有一個例外：在轉移控制權到一個 conforming 的 segment 中的時候，CPL 並不會變為新的 segment 的 DPL，而還是維持呼叫者的 CPL。
* **分段特權等級（Descriptor Privilege Level, DPL）：一個 segment（或 gate）的特權等級，存放在 segment descriptor 中的 DPL 位元**。一段程式在存取一個 segment（或 gate）時，處理器會比較 CPL 和 segment 的 DPL，及 segment selector 的 RPL。對不同型態的 segment，處理器的處理方式也不大相同：
  * 資料 segment 的 DPL 指出存取該 segment 所需要的最低特權等級。例如，若一個資料 segment 的 DPL 為 2，則程式的 CPL 必需是 0、1、或 2 才能存取這個 segment。
  * Non-conforming 的程式 segment 的 DPL 指出「直接呼叫」（不經由 call gate）時，呼叫者所需要的特權等級。例如，若一 non-conforming 的程式 segment 的 DPL 為 1，則只有 CPL 為 1 的程式才能直接呼叫它。
  * Call gate 和 TSS 的 DPL 指出存取該 gate 所需要的最低特權等級。它們的規則和資料 segment 是一樣的。
  * 經由 call gate 呼叫程式 segment 時，其 DPL 指出呼叫者所容許的最高的特權等級。例如，若一程式 segment 的 DPL 為 2，則只有 CPL 為 2 和 3 的程式才能透過 call gate 呼叫它。
* **要求特權等級（Requested Privilege Level, RPL）：在存取一個 segment 時，可以在 segment selector 中指定 RPL（參考「記憶體管理」的「分段架構」），來要求以某個特定的特權等級來存取該 segment**。處理器在檢查特權等級是否相符時，會以 CPL 和 RPL 中較低者來決定。所以，即使 CPL 的等級可以存取某個 segment，若 RPL 的等級不足，還是不能存取該 segment。
  * RPL 可以用來避免系統程式以不正確的特權等級存取某個 segment。例如，某個作業系統中的 API 會把一些資料寫到使用者程式提供的 segment 中。假設系統 API 在 ring 0 中執行，而程式在 ring 3 中執行。若沒有特別檢查，則使用者可以把一個 DPL 為 0 的 segment（使用者程式不能存取它）傳到該 API 中，因為 API 有寫入該 segment 的權力，因此使用者程式就可以破壞該 segment 中的資料。為了避免這個問題，系統 API 在存取使用者傳入的 segment 時，可以先把 segment selector 的 RPL 設定成和使用者程式的 CPL 相同，就不會意外寫入原先使用者無權存取的 segment 了。

**特權等級的檢查，是在把 segment selector 載入分段暫存器的時候進行的**。對堆疊 segment 而言，雖然在性質上類似資料 segment，但是堆疊 segment 的 segment selector 在載入 SS 暫存器時，其 CPL 和 segment 的 DPL 必需相同（如同 non-conforming 的程式 segment 一般），否則會導致 general-protection fault（\#GP）。

## 控制權轉移

程式可以經由執行 JMP、CALL、RET、INT n、和 IRET 指令來轉移控制權。在處理器發生例外（exception）、或是中斷（interrupt）、及 IRET 指令是比較特別的（參考「中斷／例外處理」）。

**除了中斷和例外之外，只有 JMP、CALL 和 RET 可以進行跨 segment 的跳躍**。在同一個 segment 中的跳躍（如 near 的 JMP、CALL、RET 和各個條件分支指令）並不會有任何限制。**但是在跨 segment 的跳躍時，就會檢查相關的權限是否允許進行跳躍**。跨 segment 的跳躍有以下幾種情形：

* 直接跳躍到另一個程式 segment
* 經由 gate

直接跳躍到另一個程式 segment，當使用 CALL 或 JMP 直接跳躍到另一個程式 segment 時，會檢查目前的 CPL、目標 segment descriptor 的 DPL、目標 segment selector 的 RPL、和目標 segment descriptor 的 C 旗標。如果 C 旗標是 0，表示目標 segment 是一個 nonconforming 的程式 segment。在跳躍到 nonconforming 的 segment 時，CPL 一定要和目標的 DPL 相同，而且 RPL 一定要小於或等於 CPL，否則會導致 general-protection fault（\#GP）。而且，在跳躍到 nonconforming 的 segment 時，CPL 並不會改變，即使 RPL 比較小也是一樣。

如果 C 旗標是 1，表示目標 segment 是一個 conforming 的程式 segment。在跳躍到 conforming 的程式 segment 中時，CPL 可以大於（權力較低）或等於 DPL；只有在 CPL 小於 DPL 時，才會導致 general-protection fault（\#GP），而 RPL 則沒有任何影響。即使是在跳躍到 DPL 比較高的 conforming 的 segment 時，CPL 也不會改變，且因為 CPL 沒有改變，也不會進行堆疊切換（stack-switch）。

在作業系統中，可以把一些不會使用系統保護的部分的 API（例如，大部分的數學函式、和例外處理程式），設成 conforming 的 segment。這樣，一般的應用程式就可以直接使用這些 API。在呼叫這些 segment 時，CPL 並不會改變，可以避免在一個應用程式呼叫一個 DPL 權力較高的 segment 時，以較高的權力改變了某些應用程式不能改變或存取的地方。

大部分其它的程式 segment 都應該設定成 nonconforming。

經由 gate：如果一個應用程式無法呼叫權限（DPL）較高的程式 segment，那麼要怎麼讓程式呼叫系統的 API（通常權限較高）呢？答案是：經由 gate 呼叫。Gate 是一種系統 segment，而它的 segment descriptor 稱為 gate descriptor。

Gate 有四種：call gates、trap gates、interrupt gates、和 task gates。在這裡，只討論 call gates；trap gates 和 interrupt gates 在「中斷／例外處理」中會說明，而在「多工處理」中會說明 task gates。利用 call gate，才可以在不同權限的程式之間來回。



 

