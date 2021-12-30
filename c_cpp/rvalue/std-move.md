# std::move

## move語義

移動語義的意義：資源所有權的轉讓。即進行資料複製的時候，將動態申請的記憶體空間的所有權直接轉讓出去，不用再另外建立空間進行大量的資料拷貝，既節省空間又提高效率。

被移動的資料交出了所有權，為了不出現解構兩次同一資料區，要將交出所有權的資料的指向動態申請記憶體去的指標賦值為nullptr，即空指標。對空指標執行delete\[\]是合法的。

我們可以用標準庫中的std::move函式來表示一個對象可以被移動，即交出資源所有權。它可以將傳入的引數轉化為右值引用：

```cpp
#include <iostream>

using std::cout;
using std::endl;

void f(int && x) {
  cout << "rvalue x= " << x << endl;
}

void f(const int & x) {
  cout << "const lvalue x= " << x << endl;
}

int main() {
  int s = 4;
  f(s);            // invoke f(&), lvalue
  f(std::move(s)); // invoke f(&&), rvalue
  return 0;
}
```

再比如對於只允許移動的對象\(獨佔資源，如unique\_ptr\)，可以通過std::move與move ctor來轉移資源的所有權：

```cpp
#include <iostream>
#include <string>
#include <memory>

int main() {
  auto p1 = std::make_unique < std::string > ("hello");
  std::cout << * p1 << std::endl;    // hello
  auto p2 = std::move(p1); // p1管理的資源的所有權交給p2

  // error, 因為p1已經將所有權交出，不可再被存取
  // cout << *p1 <<endl;
  std::cout << * p2 << std::endl; // hello
  return 0;
}
```

