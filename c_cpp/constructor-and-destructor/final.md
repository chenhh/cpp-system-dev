# final關鍵字

C++11後開始支援。

final關鍵字可用於修飾class或virtual function。

* 用在修飾class時，表示該class不能被繼承。
* 用在修飾virtual function時，表示該virtual function不能被override。

### final用在修飾class時，表示該class不能被繼承

```cpp
#include <iostream>

using std::cout;
using std::endl;

// 加上final關鍵字後，class不能被繼承
class Base final {
  public: virtual void Foo(int data) {
    cout << "Base::Foo()" << endl;
  }
};

// 試圖繼承一個final class, 會發生編譯錯誤 
class Derived: public Base {
  public: virtual void Foo(int data) override {
    cout << "Derived::Foo()" << endl;
  }
};
int main() {
  Base * pf = new Derived();
  pf -> Foo(50);
  delete pf;
  return 0;
}
```

### final用在修飾virtual function時，表示該virtual function不能被override

```cpp
#include <iostream>

using std::cout;
using std::endl;

class Base {
  public:
    // final virtual function, 不可被override
    virtual void Foo(int data) final {
      cout << "Base::Foo()" << endl;
    }
};

class Derived: public Base {
  public:
    // 試圖override final virtual function, 會發生編譯錯誤 
    virtual void Foo(int data) override {
      cout << "Derived::Foo()" << endl;
    }
};
int main() {
  Base * pf = new Derived();
  pf -> Foo(50);
  delete pf;
  return 0;
}
```
