# CMake

CMake允許開發者編寫一種平台無關的CMakeList.txt文件來定制整個編譯流程，然後再根據目標用戶的平台逐步生成所需的本地化Makefile和工程文件。

當編譯一個需要使用第三方庫的軟體時，我們需要知道以下檔案的位置：

* 去哪兒找標頭檔案 .h   \(對比gcc的-I參數\)
* 去哪兒找庫檔案 \(.so/.dll/.lib/.dylib/...\)  \(對比gcc的-L參數\)
* 需要連結的庫檔案的名字  \(對比gcc的-l參數\)

