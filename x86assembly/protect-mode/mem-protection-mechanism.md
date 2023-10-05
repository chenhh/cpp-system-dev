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

**特權等級的檢查，是在把 segment selector 載入分段暫存器的時候進行的**。對堆疊 segment 而言，雖然在性質上類似資料 segment，但是堆疊 segment 的 segment selector 在載入 SS 暫存器時，其 CPL 和 segment 的 DPL 必需相同（如同 non-conforming 的程式 segment 一般），否則會導致 general-protection fault（#GP）。

### 特權指令

有一些系統方面的指令，只有特權等級為 0（權限最高）的程序才可以使用。如果一個 CPL 不為 0 的程序試圖執行這些指令，會導致 general-protection fault（#GP）。其中的一些指令則可以設定為可在 CPL 不為 0 的程序中執行。

下面列出這些特權指令：

* LGDT － 載入 GDTR。
* LLDT － 載入 LDTR。
* LTR － 載入工作暫存器（task register）。
* LIDT － 載入 IDTR。
* MOV（控制／除錯暫存器） － 載入或儲存控制／除錯暫存器（CRx 和 DRx）。
* LMSW － 載入 MSW（Machine Status Word）。
* CLTS － 清除 CR0 中的 task-switched 旗標。
* INVD － 將 cache 設為無效，不寫回資料。
* WBINVD － 將 cache 設為無效，寫回資料。
* INVLPG － 將 TLB 的 entry 設為無效。
* HLT － 停止處理器動作。
* RDMSR － 讀取 MSR（Model-Specific Registers）。
* WDMSR － 寫入 MSR。
* RDPMC － 讀取 PMC（Performance-Monitoring Counter）。
* RDTSC － 讀取 TSC（Time-Stamp Counter）。

把 CR4 的 PCE 旗標（第 4 bit）設為 1，可以使 RDPMC 指令可在任何 CPL 下執行。把 CR4 的 TSD 旗標（第 2 bit）設為 1，則可使 RDTSC 指令可在任何 CPL 下執行。

在 EFLAGS 旗標中的 IOPL（第 12 bit 和第 13 bit），設定存取輸出入位址空間（I/O address space）所需的最低權限。例如，如果 IOPL 設為 1，則只有 CPL 為 0 和 1 的程序可以執行 I/O 指令，和存取 I/O 記憶體。這個旗標只能在 CPL 為 0 的程序中，利用 POPF 或 IRET 命令更改。

&#x20;

控制權轉移


程式可以經由執行 JMP、CALL、RET、INT n、和 IRET 指令來轉移控制權。在處理器發生例外（exception）、或是中斷（interrupt）、及 IRET 指令是比較特別的（參考「中斷／例外處理」）。

**除了中斷和例外之外，只有 JMP、CALL 和 RET 可以進行跨 segment 的跳躍**。在同一個 segment 中的跳躍（如 near 的 JMP、CALL、RET 和各個條件分支指令）並不會有任何限制。**但是在跨 segment 的跳躍時，就會檢查相關的權限是否允許進行跳躍**。跨 segment 的跳躍有以下幾種情形：

* 直接跳躍到另一個程式 segment
* 經由 gate

直接跳躍到另一個程式 segment，當使用 CALL 或 JMP 直接跳躍到另一個程式 segment 時，會檢查目前的 CPL、目標 segment descriptor 的 DPL、目標 segment selector 的 RPL、和目標 segment descriptor 的 C 旗標。如果 C 旗標是 0，表示目標 segment 是一個 nonconforming 的程式 segment。在跳躍到 nonconforming 的 segment 時，CPL 一定要和目標的 DPL 相同，而且 RPL 一定要小於或等於 CPL，否則會導致 general-protection fault（#GP）。而且，在跳躍到 nonconforming 的 segment 時，CPL 並不會改變，即使 RPL 比較小也是一樣。

如果 C 旗標是 1，表示目標 segment 是一個 conforming 的程式 segment。在跳躍到 conforming 的程式 segment 中時，CPL 可以大於（權力較低）或等於 DPL；只有在 CPL 小於 DPL 時，才會導致 general-protection fault（#GP），而 RPL 則沒有任何影響。即使是在跳躍到 DPL 比較高的 conforming 的 segment 時，CPL 也不會改變，且因為 CPL 沒有改變，也不會進行堆疊切換（stack-switch）。

在作業系統中，可以把一些不會使用系統保護的部分的 API（例如，大部分的數學函式、和例外處理程式），設成 conforming 的 segment。這樣，一般的應用程式就可以直接使用這些 API。在呼叫這些 segment 時，CPL 並不會改變，可以避免在一個應用程式呼叫一個 DPL 權力較高的 segment 時，以較高的權力改變了某些應用程式不能改變或存取的地方。

大部分其它的程式 segment 都應該設定成 nonconforming。

經由 gate：如果一個應用程式無法呼叫權限（DPL）較高的程式 segment，那麼要怎麼讓程式呼叫系統的 API（通常權限較高）呢？答案是：經由 gate 呼叫。Gate 是一種系統 segment，而它的 segment descriptor 稱為 gate descriptor。

Gate 有四種：call gates、trap gates、interrupt gates、和 task gates。在這裡，只討論 call gates；trap gates 和 interrupt gates 在「中斷／例外處理」中會說明，而在「多工處理」中會說明 task gates。利用 call gate，才可以在不同權限的程式之間來回。

### call gate

![](../../.gitbook/assets/call\_gate.gif)

Call gate descriptor 可以在 GDT 或 LDT 中，不過不能在 IDT（Interrupt descriptor table）中。它指出了程式所在的 segment（由 Segment Selector 欄位指定），並指出了程式在該 segment 中的偏移量（程式的進入點），及呼叫者所需要的特權等級（由 DPL 指定）。如果需要堆疊切換，它還指出需要參數的個數。而上面的 P 則是表示 call gate 是否有效。

P 通常是設為 1，表示這是一個有效的 call gate。有時候，在某些特殊的情形下，會想要把 P 設為 0。例如，想要知道這個 call gate 被呼叫了幾次，可以把 P 設為 0，則在呼叫這個 call gate 時，會發出 not-present 例外。在例外處理程式中，把計數加一，再把 P 設為 1，讓程式繼續執行，這樣就可以追蹤這個 call gate 被呼叫的次數了。

用 CALL 命令或 JMP 命令都可以呼叫 call gate。呼叫時，把 segment selector 指向要呼叫的 call gate descriptor，而偏移量還是要指定，但是會被忽略（在 call gate 中有偏移量）。經由 call gate 呼叫時，CPL 和 RPL 都要小於 call gate 的 DPL；也就是說，call gate 的 DPL 指定「可容許的最高權限」。如果呼叫者的 CPL（或 RPL）比 call gate 的 DPL 還小（權限更高），則不能呼叫。

在使用 CALL 命令呼叫 call gate 時，無論目標 segment 是否為 conforming，該 segment（非 call gate）的 DPL 都必須小於或等於呼叫者的 CPL。但若用 JMP 命令呼叫 call gate 時，在目標 segment 是 conforming 時，和使用 CALL 命令呼叫時相同。但是若目標 segment 是 nonconforming，則目標 segment 的 DPL 必須和呼叫者的 CPL 相同，才能呼叫。也就是說，只能用 CALL 命令呼叫權限較高的程式，而不能用 JMP 跳到權限較高的程式碼中。

利用 call gate，可以在同一個程式 segment 中的各個程序設定不同的特權等級。例如，作業系統核心的程式 segment 中，有些程序是可供應用程式呼叫的 API；有些則是內部使用的程序，不希望由應用程式呼叫。這時，就可以把 API 的 call gate 的 DPL 設為 3，而將內部程式的 call gate 的 DPL 設為 1 或 0。

在經由 call gate 呼叫，將控制權轉移到新的程式 segment 之後，CPL 會變成和程式 segment 的 DPL 相同。

### 堆疊切換

為了避免權限較高的程式被權限較低的呼叫者影響，在 CPL 改變時，處理器會進行堆疊切換。每一個 task 對所有用到的特權等級，都要維護分開的堆疊。在 ring 3 中的堆疊指標，是直接存放在 SS 和 ESP 中；而 ring 2、ring 1、ring 0 的堆疊指標（由一個 segment selector 和一個 offset 組成）則存放在 TSS 中。當 CPL 變小時，處理器會載入 TSS 中相對映的堆疊指標，切換到新的堆疊。這三個堆疊指標在程式執行途中是不能改變的。當然，如果作業系統只用到兩個特權等級（例如，只用到 ring 0 和 ring 3），則可以只維護兩個堆疊。在特權等級較高的程序返回到 ring 3 的程序時，會回復原來的 SS 和 ESP。因為在切換特權等級時，會自動進行堆疊切換，所以即使作業系統不使用多工處理的能力，也必須定義一個 TSS，來指定這些堆疊。

作業系統必須負責維護適當的堆疊空間給各個權限使用，並在 TSS 中指定適當的堆疊指標。堆疊的大小必須夠大，至少要能放得下呼叫者的 SS、ESP、CS、EIP，和程序的參數，及程序在執行時所需的區域變數。如果程序是中斷或例外處理程式，則還要能放下 EFLAGS 和錯誤碼。

由於在呼叫權限較高的程序時，會有堆疊切換，因此還必須把呼叫者所推到堆疊中的參數複製到新的堆疊中。在 call gate 中的「參數個數」欄位，就是用來指定參數的個數（最多可達 31 個）。處理器在堆疊切換時，會依序把呼叫者的 SS、ESP、參數、CS、和 EIP 推到新的堆疊中。如果程序所需要的參數超過 31 個，則可以傳入一個指標，指向一塊資料結構，或是利用呼叫者的 SS 和 ESP 來取得呼叫者的堆疊中存放的參數資料。

### 由程序中返回

在程序中，遇到 RET 指令時，會回到呼叫者程序中。近程（在同一 segment 中）的 RET 指令，因為沒有牽涉到權限的改變；因此，處理器只會進行邊界檢查，確保堆疊中存放的返回位址是在 segment 的範圍之內。但遠程的 RET 指令可能會需要在返回時，同時改變 CPL；因此，除了邊界檢查之外，處理器還會檢查返回的目標的 segment 的權限（即該 segment 的 DPL）是否大於或等於目前的 CPL。因為，只有權限較低的程序可以呼叫權限較高的程序，因此在返回時，目標 segment 的權限必然是比較低的。如果不是的話，就表示堆疊中返回位址是不正確的。

在返回時，處理器利用堆疊中存放的 CS 中的 RPL 值來判斷是否需要改變權限。如果 RPL 的值比目前的 CPL 還大（權限較低），則表示在返回時，需要改變權限。如果需要改變權限，則同時也需要切換堆疊。因此，處理器會在載入 CS、EIP 之後，再載入堆疊中存放的 SS、ESP（在呼叫程序時推入堆疊中，參考上節說明）。當然，處理器還是會檢查 SS 和 ESP 是否合法。最後，處理器會檢查各個資料分段暫存器（DS、ES、FS、GS）是否指向 DPL 比新的 CPL 更小（權限更高）的 segment。處理器會把指向 DPL 較小的分段暫存器設為 null selector；不過，指向一個權限較高的 conforming 的程式碼 segment 原本就是合法的，所以如果某個資料分段暫存器是指向 conforming 的程式碼 segment，則不會被設為 null selector。

## 分頁保護

除了分段形式的保護機制之外，還有分頁的保護機制。分頁的保護機制是以分頁為單位，有兩個特權層次：一是 supervisor 等級（等級 0）、一是 user 等級（等級 1）。分頁保護和分段保護一樣，是在存取記憶體位址之前進行的。如果違反了分頁保護，則不會存取記憶體的內容，而且會導致 page-fault（#PF）的例外。

分頁保護除了等級的保護之外，還有讀寫權的保護。一個分頁若設定為唯讀，就不能把資料寫到這個分頁中了。

在分頁目錄和分頁表的 entry 中，有兩個旗標：R/W 旗標和 U/S 旗標，分別代表分頁的讀寫權和特權層次。這兩種保護機制對分頁目錄和分頁表都有效。

### 分頁的特權層次&#xD;

在分頁目錄和分頁表的 entry 中，U/S 旗標表示一個分頁的特權層次。若 U/S 為 0，則分頁是 supervisor 等級，否則為 user 等級。所有的程序都可以存取 user 等級的分頁，但是只有 CPL 為 0、1、2 的程序才可以存取 supervisor 等級的分頁。因此，我們把 CPL 為 3 的程序稱為 user 模式；而把 CPL 為 0、1、2 的程序稱為 supervisor 模式。在一個簡單的系統中，可能會使用最簡單的分段方式，即把所有的資料、程式分段都混合在一起。這時，分頁保護就可以提供最基本的保護。把系統程式放在 supervisor 等級的分頁，而把使用者程式放在 user 等級的分頁，就可以做到最起碼的保護能力了。

分頁的特權層次，是由分頁目錄和分頁表的特權合併而成的。在分頁目錄或分頁表中，其中一個的等級是設為 supervisor 時，則分頁的等級就視為 supervisor。只有在分頁目錄和分頁表中的特權層次都是 user 時，分頁的等級才是 user。在讀寫權的設定也是一樣。只有在分頁目錄和分頁表的讀寫權都是可任意讀寫時，分頁才可以任意讀寫；否則，分頁會被視為唯讀的分頁。

### 分頁的讀寫權&#xD;

把分頁的 R/W 旗標設為 0，表示分頁是唯讀的，否則表示分頁是可以任意寫入的。當 CR0 中的 WP 旗標（第 16 bit）設為 0 時，對 supervisor 等級的程式來說，所有的分頁都是可任意讀寫的，即分頁的 R/W 旗標只對 user 等級的程式有效。而在 WP 旗標設為 1 時，則 R/W 旗標對所有等級的程式都有效；也就是說，即使是 supervisor 等級的程式，也不能寫入唯讀的分頁。

這個功能可以在某些場合中，節省記憶體的使用。例如，在 UNIX 作業系統中，使用 fork 函式來建立子程序時，必須把資料節區複製一份給子程序。但是，利用這個功能，系統可以先不複製節區，而把母程序的節區的分頁對映到子程序，讓子程序使用，但是把子程序中的分頁設為唯讀。當子程序試圖寫入其中一個分頁時，會產生例外，這時系統才需要為子程序建立一個自己的分頁，並把資料複製一份。這樣就可以節省很多記憶體和時間。

### 分頁保護和分段保護&#xD;

處理器在存取記憶體時，會先考慮分段保護。如果分段保護的設定允許讀取動作，處理器才會考慮分頁保護。只有在分段保護和分頁保護的檢查都通過時，處理器才會存取記憶體的內容。因此，分頁保護的設定並不能取代分段保護的設定。分頁保護可以用來加強分段保護，在一個可任意讀寫的分段中，可以把某些分頁設定為只能讀取。

在某些特殊情形下，分頁保護會被忽略，如同是由 CPL 為 0 的程式存取一般。這些情形包括：

存取 GDT、LDT、或 IDT 的內容。

呼叫權限較高的程序，或是發生中斷（或例外）時，存取內部的堆疊。

## 邊界檢查

邊界檢查是用來確保所有對 segment 的存取動作，都在 segment 的有效範圍內。一個 segment 的有效範圍，由 segment descriptor 中的參數來決定。決定有效範圍的方式如下：

若 B = 0，則範圍的最大值 MAX 為 FFFFH；若 B = 1，則 MAX 為 FFFFFFFFH。

若 G = 0，則有效邊界 LIMIT 即為 descriptor 中的邊界值，即有效邊界可以從 0 到 FFFFFH。若 G = 1，則有效邊界的最左端 12 bit 不被檢查，即有效邊界可以從 FFFH 到 FFFFFFFFH；也就是說，若邊界值是 0，有效邊界 LIMIT 則是 FFFH，若邊界值是 1，則 LIMIT 是 1FFFH。

若 segment 是資料 segment，而且其 E = 1，則有效範圍是 LIMIT + 1 到 MAX。若 E = 0 或 segment 是程式碼的 segment，則有效範圍是 0 到 LIMIT。

任何試圖存取在有效範圍之外的動作，都會導致例外（exception）。所以，試圖在 LIMIT - 1 的地方讀取一個 word（16 bit），也會導致例外。

除了對一般的 segment 有邊界檢查之外，對 GDT 和 IDT 也會進行邊界檢查。在 GDTR 和 IDTR 中存放的邊界值可以避免存取到 GDT 和 IDT 外面的值。同樣的，在 LDTR 和工作暫存器（task register）中也有存放從 segment descriptor 中讀取的邊界值，所以對 LDT 和 TSS 也會進行邊界檢查。

## 型態檢查

型態檢查是用來避免對 segment（或 gate，gate 是一種系統 segment）進行不適當的動作，例如把資料 segment 當成程式執行。Segment 的型態由 S 旗標和型態位元決定（參考「記憶體管理」的「分段架構」）。

型態檢查有下面幾種（這裡只是舉一些例子）：

分段暫存器的檢查：

* 只有程式碼的 segment selector 能被載入到 CS 中。
* 不能讀取的程式 segment selector（R = 0）不能載入到 DS、ES、FS、GS 中。

只有能寫入的資料 segment selector（W = 1）被載入到 SS 中。

不能把 null segment（GDT 的第 0 個 segment descriptor）載入到 CS 或 SS 中。

LDTR 或工作暫存器的檢查：

只有 LDT 的 segment selector 能被載入到 LDTR 中。

只有 TSS 的 segment selector 能被載入到工作暫存器中。

存取 segment 中的資料時的檢查：

不能把資料寫入一個可執行的 segment（程式碼 segment）。

不能把資料寫入 W = 0（不允許寫入）的資料 segment。

不能讀取 R = 0（不允許讀取）的程式碼 segment 中的資料。

不能對 null segment 進行存取。

指令中有分段暫存器時的檢查：

跨段的（far）CALL 或 JMP 指令，只能到程式碼 segment、call gate、task gate、或是 TSS。

載入一些系統暫存器的指令（如 LLDT、LTR、LAR、LSL）所參考的 segment descriptor 的型態必須相符。

IDT 的 entry 必須是 interrupt gate、trap gate、或是 task gate。

&#x20;



&#x20;
