# 資料鏈結層\(data link layer\)

兩個實際相連的設備間

此層的主要功能為：

* 把上層的datagram加頭加尾封裝成frame
* 定義媒介上的存取方式
* 使用實際位址 \(physical addresses\)，如網卡的MAC address。
* 實際連接兩點之間的可靠傳輸，如在傳送端和接收端的流量控制。
  * 錯誤偵測 \(Error Detection\) 
  * 錯誤修正 
  * parity check, checksum

## 多重存取協定 \(multiple access protocol\)

單一共享頻道上有兩個或以上節點要同時存取資料

多重存取協定可分為以下幾類：

* 分割頻道 \(Channel Partitioning\)
  * 把頻道分成許多小塊 \(例如.時間槽, 頻率\)
  * 分配小塊給節點使用
* 隨機存取 \(Random Access\)
  * 允許發生碰撞
  * 重點在發生碰撞後的處理動作
* 輪流

  * 嚴格地調節共享媒介的使用，以不發生碰撞為目的

### 頻道分割MAC協定: TDMA \(time division multiple access\)

* 每一個節點可以得到固定長度的slot\(長度 = 封包傳輸時間\)
* 沒用到的slot稱為idle 
* 範例: 6-station LAN, 1,3,4有封包, slots 2,5,6 idle 
* TDM \(Time Division Multiplexing\): 頻道分割成 N個時間槽, 每個節點使用一個

![TDMA](../.gitbook/assets/tdma.png)

### 頻道分割MAC協定: FDMA\(frequency division multiple access\)

* 頻譜分割成幾個頻帶
* 範例: 6-station LAN, 1,3,4有封包, 頻帶2,5,6idle 。

![FDMA](../.gitbook/assets/fdma%20%282%29.png)

### 頻道分割MAC協定：CDMA \(code division multiple access\)

* 指定唯一的code給每一個使用者，是為分割code set
* 所有的使用者分享一樣的頻率但是使用自己的code去編碼
* encoded signal = \(original data\) X \(chipping sequence\)
* decoding: inner-product of encoded signal and chipping sequence
* 允許許多使用者同時傳送資料

![CDMA&#xFF0C;&#x6709;&#x5169;&#x8005;&#x4F7F;&#x7528;&#x8005;&#x4F7F;&#x7528;&#x6B63;&#x4EA4;&#x7684;&#x7DE8;&#x78BC;&#x50B3;&#x9001;&#x8A0A;&#x865F;](../.gitbook/assets/cdma.png)

### Slotted ALOHA

時間分割成等長的slot

![slotted ALOHA, S\(success\), C\(collision\), E\(empty\)](../.gitbook/assets/slotted_aloha-min.jpg)

假設有N個節點要傳送封包

### pure \(unslotted\) ALOHA

設計較簡單，非同步

![pure ALOHA](../.gitbook/assets/pure_aloha-min.png)




















