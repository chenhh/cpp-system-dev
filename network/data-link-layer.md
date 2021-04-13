# 資料鏈結層\(data link layer\)

兩個實際相連的設備間，如：host-router, router-router, host-host。以訊框\(frame\)為資料的單位。

此層的主要功能為：

* 把上層的datagram加頭加尾封裝成frame
* 定義媒介上的存取方式
* 使用實際位址 \(physical addresses\)，如網卡的MAC address。
* 實際連接兩點之間的可靠傳輸，如在傳送端和接收端的流量控制。

  * 錯誤偵測 \(Error Detection\)     ：訊號衰減和雜訊會造成錯誤發生    。當接收端發現錯誤，會通知傳送端重送或是把訊框給丟掉    。
  * 錯誤修正     ：由傳送端辨別並修正錯誤的位元，避免重送    。
  * parity check, checksum

## 多重存取協定 \(multiple access 





