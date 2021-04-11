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

## IPv6

