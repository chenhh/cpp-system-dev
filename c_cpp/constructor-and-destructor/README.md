# 建構子與解構子(constructor and destructor)

## 解構子(destructor)

* 解構函式 (destructor) 為程式結束執行銷毀物件之用，通常編譯器會提供預設的解構函式，可是當類別成員有指標(指向new產生的物件)的時候，就需要自行撰寫解構函式，使其進行有效的記憶體管理。
* **解構函式中不可丟出異常**，否則會造成stack unwinding的混亂。

### virtual destructor&#xD;

如果你使用了一個多型物件，而沒有為它的解構子帶上 virtual 的宣告的話，會有兩種類型的錯誤會發生。

* 只執行 Base 的解構子
* 編譯器不知道釋放多少空間，而產生的 Undefined Behavior

**不要繼承沒有虛擬解構子的 class ，像是 STL 中的 string 與 container，否則會有意想不到的錯誤在等著你**。

### 錯誤1: 只執行Base的解構子

new生成的是Derived物件，但是因為Derived的解構子沒有加上virtual關鍵字，所以Derived的解構子不會被正常呼叫。

```cpp
#include <iostream>

using namespace std;

class Base {
  public:
  // 必須在此加上virtual關鍵字，才能正確執行
    ~Base() {
      cout << "Base desturctor" << endl;
    };
};
class Derived: public Base {
  public:
  // 為了可讀性，此處應該也要加上virtual關鍵字
    ~Derived() {
      cout << "Derived destructor" << endl;
    };
};
int main() {
  Base * ptr = new Derived();
  delete ptr; // Base destructor
  return 0;
}
```





