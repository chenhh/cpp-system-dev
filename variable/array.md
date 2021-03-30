# 陣列\(array\)

## 一維陣列

### array of pointers

陣列內存放的是指向`const char*`的指標。

```cpp
#include <stdio.h>

int main(){
    // array of pointers
    const char* data[2] = {"USA", "TW"};
    for(size_t idx = 0; idx < 2; ++idx){
        printf("%s\n", *(data+idx));
    }
    return 0;
}
```

## 指標陣列

## 二維陣列

