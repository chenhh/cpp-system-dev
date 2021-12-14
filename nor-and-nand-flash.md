# NOR與NAND Flash

## EPROM

Intel很早就發明了EPROM，這是一種可以用紫外線擦除的存儲器。相較於ROM，它的內容可以更新而且可以保持10～20年，老式電腦的BIOS都存儲於此。

它的頂部必須被覆蓋住，以防被陽光裡的紫外線擦除。後來Intel在其基礎上於1978年發明了電可擦除的升級版叫做EEPROM。不需要陽光的幫忙，方便多了，可是讀取和擦除速度卻非常緩慢。

## NOR and NAND Flash

* 都是非易失存儲介質。即掉電都不會丟失內容。
* 在寫入前都需要擦除。實際上NOR Flash的一個bit可以從1變成0，而要從0變1就要擦除整塊。NAND flash都需要擦除。

|            | NOR           | NAND            |
| ---------- | ------------- | --------------- |
| 容量         | 較小            | 很大              |
| XIP(可執行程式) | 可以            | 不行              |
| 讀取速度       | 很快            | 快               |
| 寫入速度       | 慢             | 快               |
| 擦除速度       | 很慢            | 快               |
| 可擦除次數      | 10000\~100000 | 100000\~1000000 |
| 可靠性        | 高             | 較低              |
| 訪問方式       | 可隨機訪問         | 區塊方式            |
| 價格         | 高             | 較低              |

應用場景


在PC和手機上我們都可以找到NOR和NAND Flash的身影。

### NOR Flash

NOR Flash和普通的記憶體比較像的一點是他們都可以支持隨機訪問，這使它也具有支持XIP（eXecute In Place）的特性，可以像普通ROM一樣執行程式。這點讓它成為BIOS等開機就要執行的代碼的絕佳載體。



NOR Flash 根據與 Host 端介面的不同，可以分為 Parallel NOR Flash 和 Serial NOR Flash 兩類。

Parallel NOR Flash 可以接入到 Host 的控制器 上，所存儲的內容可以直接映射到 CPU 地址空間，不需要拷貝到 RAM 中即可被 CPU 訪問。NOR Flash在BIOS中最早就是這種介面，叫做FWH（Firmware HUB），由於其接是平行介面，速度緩慢，現在基本已經被淘汰。

Serial NOR Flash 的成本比 Parallel NOR Flash 低，主要通過 SPI 介面與 Host 也就是PCH相連。

現在幾乎所有的BIOS和一些機上盒上都是使用NOR Flash，它的大小一般在1MB到32MB之間，價格昂貴。

### NAND Flash

NAND Flash廣泛應用在各種存儲卡，隨身碟，SSD，eMMC等等大容量設備中。它的顆粒根據每個存儲單元內存儲位元個數的不同，可以分為 SLC（Single-Level Cell）、MLC（Multi-Level Cell） 和 TLC（Triple-Level Cell） 三類。其中，在一個存儲單元中，SLC 可以存儲 1 個位元，MLC 可以存儲 2 個位元，TLC 則可以存儲 3 個位元。

NAND Flash 的單個存儲單元存儲的位元數越多，讀寫性能會越差，壽命也越短，但是成本會更低。現在高端SSD會選取MLC甚至SLC，低端SSD則選取TLC。SD卡一般選取TLC。
