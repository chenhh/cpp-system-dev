# 檔案系統\(file system\)

## 檔案屬性\(file attributes\)

使檔案在存取的時候更加方便，且更易管理。

* Name：符號式的檔名是用人看得懂的格式儲存。
* Identifier：獨一無二的 tag \(number\)，用來辨識檔案系統內的檔案（人類不可讀）。
* Type：
* Location \(path\)：一個指標指向該檔案所在裝置的位置。
* Size：檔案大小（以字元、位元組為單位）。
* Protection：存取控制資訊，控制誰能讀、寫、執行等資料。
* Time, date, and user identification：這項資訊可以保存產生上次修改和上次使用資料，以作為保護。安全、以及使用監督。

這些資訊都存在磁碟上的目錄結構。

## 檔案操作\(File Operations\)

因為操作檔案是特權指令，使用者必須透過系統呼叫來完成檔案存取。

* Create：建立檔案。
* Write：寫入檔案。
* Read：讀取檔案。
* Reposition within file - seek：重置檔案。搜尋目錄以找到相關的進入點，然後把目前檔案位置設成某一固定值，也稱為檔案搜尋。
* Delete：刪除檔案。
* Truncate：縮減檔案。使用者可以使用此功能使檔案特性保持不變（除了檔案長度），但檔案恢復為長度 0。


