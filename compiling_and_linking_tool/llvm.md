# LLVM

LLVM的命名最早來源於底層語言虛擬機器（Low Level Virtual Machine）的縮寫。它是一個用於建立編譯器的基礎框架，以C++編寫。建立此工程的目的是對於任意的程式語言，利用該基礎框架，構建一個包括編譯時、連結時、執行時等的語言執行器。

LLVM基礎架構，是一個完整編譯器專案的集合，包括但不限於前端、後端、優化器、彙編器、連結器、libc++標準庫、Compiler-RT和JIT引擎。

編譯器的三段式架構如下：

* 前端(frontend)：將程式碼編譯成LLVM的中介碼。
* 優化器(optimizer)：針對產生的中介碼分析優化。
* 後端(backend)：對優化過的中介碼，產生指定CPU可執行的機械碼。

前端產生的LLVM IR本質上一種與程式碼和目標機器架構無關的通用中間表示方法，是LLVM專案的核心設計和最大的優勢。

三段式的架構還有一個好處，開發前端的人只需要知道如何將原始碼轉換為優化器能夠理解的中間程式碼就可以了，他不需要知道優化器的工作原理，也不需要瞭解目標機器的知識。

![LLVM三段式架構](../.gitbook/assets/llvm\_three\_stage\_arch.jpg)



## 參考資料

* [\[LLVM\] Getting Started with the LLVM System](https://llvm.org/docs/GettingStarted.html#id8)
