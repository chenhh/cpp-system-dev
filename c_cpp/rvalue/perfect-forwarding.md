---
description: std::forward
---

# perfect forwarding

有的時候，我們需要將一個函式某一組引數原封不動地傳遞給另一個函式。這裡不僅需要引數的值不變，而且需要引數的型別屬性(左值/右值，cv qualifiers)保持不變，這叫做 Perfect Forwarding（完美轉發）。

行為上，`std::forward<T>(t)` 會回傳`t`的原始型別：

* 如果引數 `t` 是左值引用 (`T` 推導成 `E&`)，那吐出來的就是 `E&` 左值引用。
* 如果引數 `t` 是右值引用 (`T` 推導成 `E&&`)，那吐出來的就是 `E&&`右值引用。

## 函數參數為左值或右值的問題

```cpp
#include <iostream>
using namespace std;

template < typename T >
  void max1(T & a, T & b, T & result) {
    result = a > b ? a : b;
  }
template < typename T >
  void max2(T & a, T & b, T & c, T & result) {
    T m;
    max1(a, b, m);
    max1(m, c, result);
  }

int main() {
  int a {10}, b {5}, c {22}, result {0};
  max2(a, b, c, result);
  cout << result << endl; //22

  // error, 因為T&只能接受lvalue
  // max2(a, 3, c, result);
  // error, 因為T&只能接受lvalue
  // max2(11, 1, c, result);
  return 0;
}
```

### 解法1: 將函數引數改為universal reference (不好的解法)

在轉換過程中，除了頂層外，內層均轉為左值，失去了轉發的意義。如果傳入的參數是大型物件，右值轉到左值的過程中，會進行拷貝的動作，相當耗費資源，理想的做法是在第一次生成右值物件後，一路轉發同物件直到函式回傳結果為止。

```cpp
// 必須使用T, U, R三個type，因為均有可能為左值或右值
template < typename T, typename U, typename R >
  void max1(T&& a, U&& b, R& result) {
    result = a > b ? a : b;
  }
// 必須使用T, U, V, R四個type，因為均有可能為左值或右值
template < typename T, typename U, typename V, typename R >
  void max2(T&& a, U&& b, V&& c, R& result) {
    R m;
    max1(a, b, m);
    max1(m, c, result);
  }
```

### 解法2：將前三個變數改為const reference可接受rvalue

這兩個解法都不夠一般化，因為都將右值轉成左值。

```cpp
// 改為const T&可接受rvalue
template < typename T >
  void max1(const T& a, const T& b, T& result) {
    result = a > b ? a : b;
  }

template < typename T >
  void max2(const T& a, const T& b, const T& c, 
            T & result) {
    T m;
    max1(a, b, m);
    max1(m, c, result);
  }
```

## Universal reference接受到的參數，如果再次使用時，會變成左值

```cpp
#include <iostream>
using namespace std;

class FishData {};

class Fish {
  public:
    Fish(const FishData & ) {
      cout << "fish copy ctor" << endl;
    }

  Fish(FishData && ) {
    cout << "fish move ctor" << endl;
  }
};

/*
為了能吃各式各樣的 FishData 左值右值，
引數 fd 宣告成 universal reference。
然後把 fd 丟進去給 Fish 的 constructor，
也是一種「轉發」，轉發了我們想用 
fd 初始化 Fish 物件的「需求」。
 */
template < typename T >
  Fish MakeFish(T && fd) {
    // universal reference不管接受的到的是左值或右值,
    // 再次使用時，均會變成左值
    return Fish(fd);
  }

int main() {
  FishData fd;
  FishData fd2;

  // l-value, "fish copy ctor"
  MakeFish(fd); 
  // r-value, 輸出與預期不符
  // "fish copy ctor" 
  MakeFish(FishData {}); 
  // r-value, 輸出與預期不符
  // "fish copy ctor"
  MakeFish(std::move(fd2)); 
  return 0;
}
```

### 完美轉發的解法

只要將上例轉發的函式修改如下，即可正確的轉發：

```cpp
// 將universal reference接受進來的變數做foward
template < typename T >
  Fish MakeFish(T&& fd) {
    return Fish(std::forward <T> (fd));
  }
```

