# x86定址模式\(addressing mode\)

## 記憶體簡介

記憶體是電腦用來存放程式與資料的地方。依讀寫能力可分為隨機存取記憶體 \(random access memory, RAM\)與唯讀記憶體 \(read only memory, ROM\)。

### 記憶體地址

以8088可定址的1M記憶體來說，為了便於管理這些容量，會將記憶體編號為00000h~FFFFh，這些編號即為**記憶體地址 \(memory address\)**。 

![8088&#x8A18;&#x61B6;&#x9AD4;&#x67B6;&#x69CB;](../.gitbook/assets/8088_mem_arch-min.png)

x86使用區段\(segment\)的方式讓作業系統管理記憶體的優點，是程式設計師不必自行管理程式放在記憶體的區塊。且CPU使用CS、DS、SS分別記錄資料\(data\)、程式\(code\)與堆疊\(stack\) 區段在記憶體中的開始位址，然後以同樣以偏移量\(offset\)方式存取讓區段中所需要元素即可。

## 立即定址\(immediate addressing mode\)

這種表示法是最簡單的，完全不用去尋找記憶體位址，就直接給定記憶體地址 \(當然這樣程式比較沒有彈性\) 。例如 `MOV AX,12h` 就是直接把12這個地址，傳送暫存器AX中。

## 直接定址\(direct addressing mode\)

直接定址模式代表指令中運算元之值為資料存放在記憶體中的位址，必須透過此位址作一次記憶體存取的動作才能取得所需的資料，又稱為絕對位址模式\(absolute addressing mode\)。

## 暫存器定址\(register operand addressing mode\)

直接將指定暫存器中的內容\(記憶體地址或數值\)，傳送給另一個暫存器。例如`MOV AX, BX`，就是將BX暫存器中的資料傳送給暫存器AX。

## 暫存器間接定址\(register indirect addressing mode\)

這種表示法，就是把暫存器裡面的數值資料，當成記憶體位址的偏移量。例如本來 BX 裡面放了 0123 這筆資料， 用了暫存器間接定址之後，就變成是指定 0123 這個地址。

例如 `MOV CX,[BX]` \(等價於`MOV CX, DS:[BX])`，就是把記憶體位址 `(DS)0 + BX` 裡面的資料，移到 暫存器CX。

* 因為8086 CPU資料匯流排寬度為16-bit，但是地址匯流排寬度為20bit，因此必須使用2個暫存器才能定址全部的記憶體實體地址。
* 此法定址的第一個暫存器如果為DS，則取出的數值視為資料；如果為CS，則取出的資料視為命令。
* 如果是邏輯地址是 `DS:[BX]`，則對應到的物理地址是`DS*16+BX。`

## 基底定址\(base addressing mode\)

類似暫存器間接定址法，只是在後面又增加了數值的偏移量。

例如 `MOV CX, [BX]+0156h`，對應到的實體地址為`(DS)0+BX+0156h`。

## 基底索引定址\(base-indexed addressing mode\)

類似base addressing mode，但是多了索引值，常用在陣列。

例如 `MOV CX, [BX+DI]+0156h`，`BX`為陣列的起始地址，而`DI`為陣列的索引值。

