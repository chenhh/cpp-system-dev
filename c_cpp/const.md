---
description: 常數或不可更改的變數
---

# const

## const關鍵字

const關鍵字有下列幾個用途：

* 欲阻止一個變數被改變，可以使用const關鍵字。在定義該const變數時，通常需要對它進行初始化，因為以後就沒有機會再去改變它。
* 對指標來說，可以指定指標本身為const，也可以指定指標所指的資料為const，或二者同時指定為const。
* 在一個函式宣告中，const可以修飾傳入的參數，表明它是一個輸入引數，在函式內部不能改變其值。
* 對於類的成員函式，若指定其為const型別，則表明其是一個常函式，不能修改類的成員變數。
* 對於類的成員函式，有時候必須指定其返回值為const型別，以使得其返回值不為“左值”。

```cpp
const classA operator*(const classA& a1,
                       const classA& a2);
// operator*的返回結果必須是一個const對象。
// 如果不是，以下的程式碼也不會編譯出錯：

classA a, b, c;
(a * b) = c; // 對a*b的結果賦值
// 操作(a * b) = c顯然沒有任何意義。
```

## CV qualifiers





## 參考資料

* [\[cpprefernece\] cv (const and volatile) type qualifiers](https://en.cppreference.com/w/cpp/language/cv)
