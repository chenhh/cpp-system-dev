# Windows PE格式

PE 檔案格式


PE 是 Portable Executable 的縮寫，也就是 Windows 系統中的可執行（Executable）程式或動態連結函式庫（Dynamic link library）的文件格式，主要使用在 32 位和 64 位的 Windows 作業系統上。

![PE格式示意圖](../../.gitbook/assets/pe\_format-min.jpg)

![32-bit PE結構 (from wiki)](../../.gitbook/assets/32\_bit\_PE-Structure2-min.png)

[Wiki有PE的SVG向量圖檔](https://en.wikipedia.org/wiki/Portable\_Executable#/media/File:Portable\_Executable\_32\_bit\_Structure\_in\_SVG\_fixed.svg)。

![機器碼對應PE結構](../../.gitbook/assets/binary\_pe-min.png)

### DOS MZ Header

開頭必定為＂MZ＂`4D 5A`，這是 DOS EXE 的特徵。而這東西讓 PE 檔案在 DOS 模式下也可執行，所佔大小為 40h bytes。

### DOS Stub

但也不是什麼都能在 DOS 底下執行，若是程式在 DOS 下無法執行便會跳出這邊的錯誤訊息：＂This Program cannot be run in DOS mode＂。

### PE Header

為了找到 PE 的開頭位置，我們可從 DOS Header 0x3C 的位置找到一個 Double word（又稱 dword，大小為 4 個 bytes），這個 dword 大小表示了整個 DOS MZ Header 的尺寸。

此處之值為 `D8 00 00 00`，因為x86使用小端儲存資料，因此大小為`000000D8h`。

到了 PE 位置所看到的必定是 `50 45`，PE Header 裡包含了許許多多的訊息，其中最重要之一就是距離 PE 位置（0xD8）後 `0x28` 的 dword，這裡是這隻程式執行時的入口點（Entry point）。聽起來好像沒什麼重要的，不過若是入口點被設置在檔案的 Overlay 區域呢？這就很有趣了～正常來說是不會設置在這邊的。

再往後數 12 Bytes 則是 Image base，最後 4 Bytes，也就是 0x01000000之前找到的入口點是相對虛擬位置（Relative virtual address, RVA）必須還得加上 ImageBase 才是真正的位置（Virtual address, VA）。

### 什麼是 RVA (relative virtual address) ?

RVA 就是虛擬空間中到參考點的距離，也就是虛擬空間中的偏移量。舉上面的例子來說：小算盤被放入虛擬地址（Virtual address, VA）的 01000000h 處，且入口點的 RVA 在 0x102D6Ch 處，那在虛擬空間之中真正入口起始位置是 0x01000000 + 0x102D6C = 0x01102D6C。

### Section Header

如果 PE file 之中有 4 個區段（section），Section Table 就會有 4 個成員，每個成員包含對應區段的屬性、記憶體大小、偏移量等訊息。

### Section

PE file 裡的內容，被劃分成各個區段，每個區段的名子以 ” . ” 當作開頭，以下是常見的區段名和作用：

| 區段名    | 功能               |
| ------ | ---------------- |
| .data  | 已初始化的資料          |
| .idata | 導入(import)的文件表名  |
| .edata | 導出(export)的文件表名  |
| .rdata | 唯讀的初始化資料         |
| .reloc | 重定位表資訊           |
| .rsrc  | 資源               |
| .text  | exe或dll文件的可執行程式碼 |

當 PE file 被執行時，PE 裝載器先檢查 DOS MZ Header 中的 PE Header 偏移量，若找到則跳到 PE Header 所在位置。接著檢查 PE header 是否有效，若有則跳到 Section Table 頭部，讀取訊息並利用文件映射（file mapping）的方法將這些區段映射到記憶體中。



