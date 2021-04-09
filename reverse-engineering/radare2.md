# Radare2

## 簡介

Radare2（也稱為r2）用於逆向工程和分析二進製文件的完整框架。由一組小型實用程序組成，可以一起使用或獨立於命令行使用。

### 起動

使用時就直接打 `r2 binary_file` 就可以進入 r2 的介面了。

一開始看到類似 shell 的畫面是 normal mode，可以輸入 ? 來看可以使用的指令。

![radare2 help&#x6307;&#x4EE4;](../.gitbook/assets/radare2_help-min.png)

也可以再進一步用 ? 看某個指令的用法。

## 常用命令

### 分析

* `a`：進行分析  ，只會看global name。
* `aa` ：進行深度分析  ，會對函式遞迴分析。
* `afl`： 可以列出所有函式  ，必須先使用過`a`或`aa`才可分析。 
* `afn`：更改函式名稱。
* `afvn`：更改區域變數的名稱。

### 搜尋

* `s`：移動到某個地址或函數。
  * `s {address}`
  * `s {function name}`

### 列印

* `pd`：印出當前命令開始後n行的指令。例如：`pd 3` \#印出後3行指令。
* `pdc`：印出從當前函式的 C-like pseudo code。一定要在函式的開頭才能用。
* `pdf` ：可以列出該函式的組合語言，與 objdump 功能類似。

### 專案

radare2 的專案管理，可以在標記完符號以後儲存在專案 ，下次就直接開啟同一個專案即可。

* `Ps {project name}`：儲存當前專案。
* `Po {project name}`：開啟專案。
* `PS {script name}`：將當前所有的操作 \(主要是 symbol 上的\) 儲存成腳本。

### 離開

* `q`：退出 r2 ，或者是退出某個模式。

### Visual Mode

visual mode 可以看到 binary 的圖形。

* `V`：打一次 V 可以看到 binary 的 hexdump，再輸入一次 V 可以看到圖形。

## 參考資料

* [官方網站](https://www.radare.org/r/)
* [Radare github](https://github.com/radareorg)
* [Radare2 book](https://book.rada.re/index.html)
* [Radare2 cheatsheet](https://scoding.de/uploads/r2_cs.pdf)

