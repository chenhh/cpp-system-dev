# 載入程式\(loading program\)

## 載入程式的工作 

* **配置記憶體空間 \(allocation\)**：要求作業系統配置空間供程式載入使用。
* **連結\(linking\)**：解決不同程式段之間彼此相互參考的問題，即解決「外部符號」\(external symbol\)的引用問題。「外部符號」是指不在本地程式段內定義之符號。
* **重定址\(relocation\)**：調整目的程式中與記憶體相關的指令或資料使程式能載入不同於原先載入的位置。
* **載入\(loading\)**：將程式載入主記憶體中 。

只要能夠完成上述第四項「載入」工作之程式，便是載入程式；並不是所有的載入程式都能完成以上四項工作 。

## 載入程式的分類 

依其功能及特性的不同可分為四種：

* 絕對載入程式\(absolute loader\)
* 組譯並執行載入程式\(assemble-and-go loader\)
* 可重定址載入程式\(relocatable loader\)
* 連結編輯程式\(linkage editor\)

### 絕對載入程式

絕對載入程式僅負責載入工作，另外三項工作負責單位如下：

* 配置記憶體空間：程式設計師。
* 連結：程式設計師。
* 重定址：組譯程式。

因為程式設計師必須負責配置記憶體空間及連結二項工作，因此對程式設計師而言具有較大的自主權；但相對地，程式設計師必須牢記副程式的位址及安排目的碼的載入位址，所以負擔很大。

絕對載入程式無法自行處理重定位問題，早期的組合語言程式為此模式。

### 組譯並執行載入程式 

* 此類載入程式為**直譯式**作法。
* 當原始程式被翻譯成機器語言後就直接被放置在主記憶體內，待原始程式全部翻譯完成，便馬上開始程式的執行動作。
* 若採用「組譯並執行載入程式」，當程式要執行時，每次都必須重新翻譯，因此執行效率較差 。

### 可重定址載入程式 

此類載入程式允許程式段可調整載入記憶體中之地址 。

### 連結編輯程式 

* 對不同程式段合併執行連結\(linking\)動作並產生產生「已連結程式」\(linked program\)或稱為「載入模組」\(load module\)。
* 當程式執行時才處理重定址及載入工作，具有較佳的執行效率之特性 。

## 動態載入\(dynamic loading\)

「動態載入」是指將程式的載入的動作，延遲到程式執行的時候才處理，真正會被執行到的程式才做載入的動作。可利用覆疊\(overlay\)及虛擬記憶體\(virtual memory\)技術來達成。











