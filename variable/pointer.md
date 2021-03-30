---
description: 儲存的內容為記憶體地址
---

# 指標\(pointer\)

## 儲存的內容為記憶體地址

![](../.gitbook/assets/pointer.jpg) 

```cpp
#include<iostream>
using std::cout;
using std::endl;

int main(){
    int *pc = nullptr, c=5;
    cout << "value of c:" << c <<endl;
    cout << "address of c:" << &c <<endl;

    pc = &c;
    cout << "value of pc:" << pc <<endl;
    cout << "value of the address of pc:" << *pc <<endl;

    c = 11;
    cout << "value of pc:" << pc <<endl;
    cout << "value of the address of pc:" << *pc <<endl;

    *pc = 2;
    cout << "value of c:" << c <<endl;
    cout << "address of c:" << &c <<endl;
    return 0;
}

```

* 指標儲存的是記憶體位址，初始化時建議設定為NULL或0\(in C\)或nullptr\(C++11之後\)。
* 變數前加上`&`符號表示取變數的記憶體地址，可設值給指標。
* 指標變數`pc`儲存的是記憶體地址，而`*pc`為指向記憶體地址的數值或變數。

## 指標與const

```cpp
#include <stdio.h>

int main() {
  const int a1 = 1; // const int variable，不可修改
  //a1 = 3;         // error, 不可修改
  int
  const a2 = 1;    // const in variable
  // a2 = 3;       // error, 不可修改

  int val = 3, val2 = 5;
  const int * ap = & val; // pointer to const int, 指標可改，變數不可改
  // *ap = 4;      // error, 指標指向的變數為const, 不可修改
  ap = & val2;     //ok, 指標可指向其它變數
  val2 = 10;
  printf("%d\n", * ap); // 10

  int val3 = 4, val4 = 7;
  int* const ap2 = & val3; // const int pointer, 指標不可改，變數可改
  //ap2 = &val4;    //error, 指標不可指向其它變數
  * ap2 = val4;     // ok, 指標指向的變數可修改
  printf("%d\n", * ap2); //7

  const int* const a = &a1; // 變數與指標均不可改

  return 0;
}
```

