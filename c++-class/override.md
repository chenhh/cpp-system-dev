# override

virtual function可以用來實作多型，但是在原本C++ 98/03的virtual卻有以下3個問題：

* virtual關鍵字在derived class中可以省略。
* 不管是在base class新創一個virtual function；或是要在derived class來override base class的virtual function，都是用同一個keyword virtual。
* Derived class可能會寫錯此virtual function的signture，而compiler不會報錯。

### 問題1: virtual關鍵字在derived class中可省略

```cpp
#include <iostream>

using std::cout;
using std::endl;

class Base {
  public:
    virtual void Foo() {
      cout << "Base::Foo()" << endl;
    }
};

class Derived: public Base {
  public:
    /*virtual*/ void Foo() {
      cout << "Derived::Foo()" << endl;
    }
};

int main() {
  Base * pf = new Derived();
  pf -> Foo(); // CDerived::Foo()
  delete pf;
  return 0;
}
```

上面程式雖然省略了virtual關鍵字，但是印出的結果為CDerived::Foo\(\)，符合預期。

但是virtual關鍵字省略的話，設計師在看Derived程式的當下無法意識到`Foo()`其實是virtual function，造成可讀性的問題。所以一般來說還是建議不管是在base class還是derived class都要幫virtual function加上virtual關鍵字。



