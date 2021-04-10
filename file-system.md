# 檔案系統\(file system\)

## 檔案屬性\(file attributes\)

使檔案在存取的時候更加方便，且更易管理。

* Name：符號式的檔名是用人看得懂的格式儲存。
* Identifier：獨一無二的 tag \(number\)，用來辨識檔案系統內的檔案（人類不可讀）。
* Type：  這項資訊對於提供不同檔案型態的系統有需要。
* Location \(path\)：一個指標指向該檔案所在裝置的位置。
* Size：檔案大小（以字元、位元組為單位）。
* Protection：存取控制資訊，控制誰能讀、寫、執行等資料。
* Time, date, and user identification：這項資訊可以保存產生上次修改和上次使用資料，以作為保護。安全、以及使用監督。

這些資訊都存在磁碟上的目錄結構。

## 檔案操作\(File Operations\)

因為操作檔案是特權指令，使用者必須透過系統呼叫來完成檔案存取。檔案\(file\) 是一個 抽象資料型態。

* Create：建立檔案。
* Write：寫入檔案。
* Read：讀取檔案。
* Reposition within file - seek：重置檔案。搜尋目錄以找到相關的進入點，然後把目前檔案位置設成某一固定值，也稱為檔案搜尋。
* Delete：刪除檔案。
* Truncate：縮減檔案。使用者可以使用此功能使檔案特性保持不變（除了檔案長度），但檔案恢復為長度 0。

## 已開啟的檔案

而對於每一個開啟的檔案也都有以下相關的資訊

* open file table：追踨已開啟的檔案。
* file pointer：對於 read 和 write 系統呼叫沒有包含檔案位移的系統而言，他們必須追蹤上一次讀/寫的位置，作為目前檔案位置的指標。
* file-open count：檔案開啟計數器紀錄開啟和關閉的次數，在最後一個關閉動作時就變成 0。系統就可以把該檔的進入點從表格移去。
* disk location&gt;. 檔案在 disk 位置存放在記憶體中，以免每一次操作都必須要從磁碟讀出。
* access right： 行程以一種存取模式開啟檔案，這個資訊會被存放在行程表中，所以作業系統可允許或拒絕輸入/輸出的需求。

## 檔案鎖\(open file locking\)



* 作業系統和檔案系統提供類似讀寫鎖\(reader-writer locks\)。
* 共享鎖\(shared lock\) 近似讀取鎖\( reader lock\)，多個行程可以同時獲得。
* 互斥鎖\(exclusive lock\) 近似於寫入鎖\(writer lock\)。
* 


