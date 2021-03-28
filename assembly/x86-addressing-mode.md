# x86定址模式\(addressing mode\)

x86使用segment的方式讓作業系統管理記憶體的優點，是程式設計師不必自行管理程式放在記憶體的區塊。且CPU使用CS、DS、SS分別記錄DATA、CODE與STACK segment在記憶體中的開始位址，然後以同樣以偏移量\(offset\)方式存取讓區塊中所需要元素即可。

## 直接定址\(immediate addressing mode\)

這種表示法是最簡單的，完全不用去尋找記憶體位址，就直接給定記憶體地址 \(當然這樣程式比較沒有彈性\) 。例如 `MOV AX,12h` 就是直接把12這個地址，傳送暫存器AX中。

## 暫存器定址\(register operand addressing mode\)

直接將指定暫存器中的內容\(記憶體地址或數值\)，傳送給另一個暫存器。例如`MOV AX, BX`，就是將BX暫存器中的資料傳送給暫存器AX。

## 暫存器間接定址\(register indirect addressing mode\)

這種表示法，就是把暫存器裡面的數值資料，當成記憶體位址的偏移量。例如本來 BX 裡面放了 0123 這筆資料， 用了暫存器間接定址之後，就變成是指定 0123 這個地址。

例如 `MOV CX,[BX]` \(等價於`MOV CX, DS:[BX])`，就是把記憶體位址 `(DS)0 + BX` 裡面的資料，移到 暫存器CX。

* 因為8086 CPU資料匯流排寬度為16-bit，但是地址匯流排寬度為20bit，因此必須使用2個暫存器才能定址全部的記憶體實體地址。
* 此法定址的第一個暫存器如果為DS，則取出的數值視為資料；如果為CS，則取出的資料視為命令。
* 如果是邏輯地址是 `DS:[BX]`，則對應到的物理地址是`DS*16+BX。`

## base addressing mode

類似暫存器間接定址法，只是在後面又增加了數值的偏移量。

例如 `MOV CX, [BX]+0156h`，對應到的實體地址為`(DS)0+BX+0156h`。

## base-indexed addressing mode

類似base addressing mode，但是多了索引值，常用在陣列。

例如 `MOV CX, [BX+DI]+0156h`，`BX`為陣列的起始地址，而`DI`為陣列的索引值。

