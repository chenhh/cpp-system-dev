# override關鍵字

## override關鍵字

我們明確地告訴編譯我們要的是override base class的virtual function，而不是在dervied class中創造一個新的virutal function。可以解決單純使用virtual function的三大問題。

```cpp
#include <iostream>

using std::cout;
using std::endl;

class Base {
  public:
    virtual void Foo(int data) {
      cout << "Base::Foo()" << endl;
    }
};

class Derived: public Base {
  public:
    // 加上override後，會在此發生編譯錯誤
    virtual void Foo(float data) override {
      cout << "Derived::Foo()" << endl;
    }
};
int main() {
  Base * pf = new Derived();
  pf -> Foo(50.5);
  delete pf;
  return 0;
}
```

## virtual function的問題

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

### 問題2:問題2: 在base class新創一個virtual function或是要在derived class來override base class的virtual function，都是用同一個virtual關鍵字

無法直接分別virtual function `Foo()`是在base class新創一個virtual function，或是在derived class來override base class的virtual function。

因為新建或覆寫函式都是用同一個virtual關鍵字，除非往base class去追踨程式通才能知道該virtual function到底是不是override base class的virtual function。

### 問題3: Derived class可能會寫錯此virtual function的signture，而編譯器不會報錯

```cpp
#include <iostream>

using std::cout;
using std::endl;

class Base {
  public:
    virtual void Foo(int data) {
      cout << "Base::Foo()" << endl;
    }
};

class Derived: public Base {
  public:
    // 不是override，而是create new virtual function
    virtual void Foo(float data) {
      cout << "Derived::Foo()" << endl;
    }
};
int main() {
  Base * pf = new Derived();
  pf -> Foo(50.5); // CBase::Foo()
  delete pf;
  return 0;
}
```

因為不同的函數簽名對編譯器而言，它就相當於建立新的virtual functon，語法上並沒有甚麼錯誤，編譯器自然不會報錯。



