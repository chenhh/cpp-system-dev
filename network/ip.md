# Internet protocol

![UDP&#x5C01;&#x5305;](../.gitbook/assets/udp_encapsulation.png)

## IPv4

IPv4是一種無連接的協定，操作在使用封包交換的連結層（如乙太網路）上。此協定會盡最大努力交付封包，意即它不保證任何封包均能送達目的地，也不保證所有封包均按照正確的順序無重複地到達。這些方面是由上層的傳輸協定（如傳輸控制協定）處理的。

IPv4使用32位元（4位元組）位址，因此位址空間中只有2^32個位址。不過，一些位址是為特殊用途所保留的，如專用網路（約1800萬個位址）和多播位址（約2.7億個位址），這減少了可在網際網路上路由的位址數量。這些限制刺激了仍在開發早期的作為目前唯一的長期解決方案的IPv6的部署。

IPv4位址可被寫作任何表示一個32位元整數值的形式，但為了方便人類閱讀和分析，它通常被寫作點分十進位的形式，即四個位元組被分開用十進位寫出如127.0.0.1，中間用點分隔。

[Linux kernel 表頭檔](https://github.com/torvalds/linux/blob/master/include/uapi/linux/ip.h)：

![IPv4&#x5C01;&#x5305;&#x8868;&#x982D;&#xFF0C;&#x901A;&#x5E38;&#x70BA;20 bytes](../.gitbook/assets/ipv4_packet-min.png)

```c
struct iphdr {
  #if defined(__LITTLE_ENDIAN_BITFIELD)
  __u8 ihl: 4,    // 4-bit for version
    version: 4;   // 4-bit for IHL
  #elif defined(__BIG_ENDIAN_BITFIELD)
  __u8 version: 4,
    ihl: 4;
  #else
  #error "Please fix <asm/byteorder.h>"
  #endif
  __u8 tos;       // 8-bit for DSCP+EPN
  __be16 tot_len; // 16-bit for total length
  __be16 id;      // 16-bit for ID
  __be16 frag_off;// 16-bit for flag and offset
  __u8 ttl;       // 8-bit for TTL
  __u8 protocol;  // 8-bit for protocol
  __sum16 check;  // 16-bit for checksum
  __be32 saddr;   // 32-bit for source addr
  __be32 daddr;   // 32-bit for dest addr
  /*The options start here. */
};
```

* IHL \(internet header length\) 4-bit, 此值為表示是由多少個32-bit word所組成，IHL &gt;=5 \(即至少5\*32 = 160 bits = 20 bytes\)，IHL &lt;=15\(最大15\*32 = 480 bits = 60 bytes\)
* version, 4-bit, value=4表示IPv4
* DSCP \( Differentiated Services Code Point\) 6-bit, 可讓您為網路傳輸指派不同的服務等級。
* ECN \(Explicit Congestion Notification, 2-bit\) 只在兩端點均支援此功能時才會使用。
  * 00 傳送主機不支援 ECN。
  * 01 或 10 傳送主機支援 ECN。
  * 11 路由器已遇到擁塞狀況。
  * 當路由器擁塞的程度已經達到填滿傳入封包緩衝區而開始捨棄封包時，會影響網路而縮減頻寬，對資料損失敏感或具時效性的傳輸量流動會進而受到衝擊，而且可能在擁塞之後產生連結閒置時間。TCP/IP 明確擁塞通知 \(ECN\) 讓路由器能夠通知「傳輸控制通訊協定」\(TCP\) 對等體，由於網路擁塞，緩衝區已滿。TCP 對等體會以減緩資料傳輸來回應，協助防止封包損失。
  * IP 表頭中的 8 位元 \[Type of Service \(TOS\)\] 欄位最先定義於 RFC 791 中，指出封包由路由器進行非預設傳送的傳送優先順序、延遲、輸送量、可靠性，以及成本等特性。
* Total length, 16-bit, 整個封包的大小 \(in bytes\), 包含表頭與資料, 20 bytes \(only header\)&lt;=total length &lt;=65535 bytes \(2\*\*16\)
  * packet size太大時，在底層會被切成較小的frame。    MTU 最大不會超過 65535 bytes。
  * 但是一般說來，過大的 IP datagram 由於傳送上的困難或是其他協定的限制，會被切割為好幾個小部分。    透過 total length 與 length 欄位的搭配，就可以計算出在 IP datagram 中資料的實際大小    。
* Identification, 16-bits:  此欄位是用來識別傳送端所發出的 IP datagram 之用，每個 IP datagram 都會有一個 unique 的 identification 值，一般來講就是以遞增值的方式來作為識別之用  。
* Flags: 3-bits & fragment offset: 13-bits  ，此兩欄位是作為進行 fragmentation 時之用  。
* time to live, TTL: 8-bits  ，這個欄位中所儲存的值，代表送出的 IP datagram 可以通過 router 數量的上限，此值由傳送端設定，每經過一個 router 會減少 1，當值減為 0 後，則會被丟棄不再繼續傳送，然後傳送端會收到 ICMP 訊息作為通知，避免傳送端一直送出無法達到目的地的封包。
* Protocol, 8-bits,    目的是在記錄使用 IP 傳送資料的是上層的哪一個協定  。
* header checksum, 16-bits  ，作為檢查 IP header 之用，跟其他協定\(例如：TCP、UDP、ICMP....etc\)的 header 無關，因為其他協定的 header 有自己的 checksum 欄位。
  * 另外，由於 checksum 是依據 IP header 中各欄位所計算出來的，因此在 IP datagram 經過 router 而 TTL 減 1 後，router 會重新計算 checksum 的值並繼續傳遞 IP datagram    。
* source IP address \(32 bits\)
* destination IP address \(32 bits\)
* options \(不定長度\)  ：最後這個欄位就比較少用到了，長度不固定，但以 4 bytes\(32 bits\) 為單位，而有可能被用在以下幾個地方：
  * 加強安全性
  * 記錄 route 資訊的
  * 記錄時間戳記
  * 指定 routing 的路徑



## IPv6

