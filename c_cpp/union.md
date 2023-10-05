# 聯合(union)

## 宣告

聯合 (union) 與結構 (structure) 有點像，但聯合內的屬性共用同一塊記憶體空間，故同一時間內僅能用聯合內其中一種屬性。聯合主要用來表示同概念但不同資料型別的實體。

```c
#include <stdio.h>

// 個別元素所佔空間最大值，即為union的所使用的空間
union sample {
    float f;    // 4 bytes
    int i;      // 4 bytes
    char ch;    // 1 byte
};

int main() {
    sample s;    
    s.i = 3.2;
    printf("size of union sample: %d\n", sizeof(s)); //4
    printf("union sample int: %d\n", s.i); //3
    s.ch = 'b';
    printf("union sample char: %d\n", s.ch); //98
    return 0;
}
```

