# 列表初始化\(list initialization\)

## 使用列表初始化的優點

* 可向上轉型，但不可向下轉型。
  * 即一個整數不能被轉換為另一個不能保持其值的整數。例如，允許將char轉換為int，但不允許將int轉換為char。 
  * 不能將一個浮點值轉換為另一個不能保持其值的浮點型別。例如，允許將float轉換為double，但不允許將double轉換為float。
*  不能將浮點值隱式轉換為整數型別。 
*  不能將整數值隱式轉換為浮點型別。

只有在使用auto關鍵字來獲取由初始化器確定的型別時，才會優先使用=而不是{}。

```cpp
#include<iostream>
using std::cout;
using std::endl;

int main(){
    auto z1 {99};   // z1 is an int
    auto z2 = {99}; // z2 is std::initializer_list<int>
    auto z3 = 99.0;   // z3 is a float number
    cout << typeid(z1).name() << endl; //i
    cout << typeid(z2).name() << endl; // St16initializer_listIiE
    cout << typeid(z3).name() << endl; // d
    return 0;
}
```



