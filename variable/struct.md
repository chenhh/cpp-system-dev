# 結構\(struct\)

## C與C++結構的比較

| C | C++ |
| :--- | :--- |
| 結構內只有單純的資料，沒有成員函數與存取權限 | 結構內可以有成員函數與存取權限，與class的差異只有在於結構內的存取權限預設為public |
| 必須使用struct關鍵字與struct\_name才能宣告變數。 | 只須用strcuct\_name即可宣告變數。 |
| struct內不可有static member | 可以有static member |
| 對於空結構，sizeof之值為0 | 對於空結構，sizeof之值為1 |

```c
#include <stdio.h>

#include <stddef.h>

struct Item {
  const char * name;
  int amount;
};

// 可以typedef方式簡化, 在宣告變數時不必加上struct
// 只要用Item宣告變數即可
// typedef struct Item Item;

int main() {
  // pure C在宣告變數時，要加上struct關鍵字，可用typedef簡化
  // C++不必加上struct，直接用Item即可
  struct Item prog = {
    .name = "Programming",
    .amount = 10
  };

  // 此種初始化struct資料的方式，在c99稱為designated initializers
  // 不用按照順序, 沒有指派的成員會被補上 0
  struct Item text = {
    .name = "novel",
    .amount = 20
  };

  struct Item items[] = {
    prog,
    text
  };
  for (size_t idx = 0; idx < 2; ++idx) {
    printf("name: %20s, amount: %d\n",
      items[idx].name, items[idx].amount);

  }
  return 0;
}
```

也可用typdef合併在struct的宣告上，不必再另外宣告。

```c
// 匿名結構的宣名, 內有*name與amount
// 將此匿名的結構以typedef定為Item
typedef struct {
  const char * name;
  int amount;
}
Item;

// 結構的名稱為Good, 以typedef將struct Good定為Item
// 可用struct Good或Item宣告變數，兩者同義
typedef struct Good {
  const char * name;
  int amount;
}
Item;
```

