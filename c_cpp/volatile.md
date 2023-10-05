# volatile

```cpp
volatile int x=10;
```

* volatile 關鍵字是一種型別修飾符，用它宣告的型別變數表示可以被某些編譯器未知的因素（作業系統、硬體、其它執行緒等）更改。所以使用 volatile 告訴編譯器不應對這樣的對象進行優化。
* volatile 關鍵字宣告的變數，每次訪問時都必須從記憶體中取出值（沒有被 volatile 修飾的變數，可能由於編譯器的優化，從 CPU 暫存器中取值）
* const 可以是 volatile （如只讀的狀態暫存器）
* 指標可以是 volatile

