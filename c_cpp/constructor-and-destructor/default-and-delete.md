# default與delete關鍵字

C++11後支援。

## default關鍵字

當我們在撰寫class時，編譯器會隱式生成一些member functions，儘管我們沒有去定義這些member functions。因為編譯器在幕後做了一些事，而從程式碼上面卻看不出來。

有了default關鍵字後，可以增加可讀性，讓我們可以知道這些member functions用的是編譯器預設建構出來的版本。

## delete關鍵字

使用delete關鍵字設定在class的member functions中，可防止編譯器生成不必要的函數。

### 一般function也可以用delete

當我們實作了`void Fuzzy(int a)`，又不希望Fuzzy function傳入char或double的時候產生implict conversion時就可以透過delete 關鍵字來產生編譯錯誤。

```cpp
#include <iostream>

using std::cout;
using std::endl;

void Fuzzy(char) = delete;
void Fuzzy(float) = delete;
void Fuzzy(double) = delete;
void Fuzzy(int a) {
  cout << a << endl;
}

int main() {
  // 以下均為compile error
  // 因為均會造成隱式轉型 
  Fuzzy('a');
  Fuzzy(1.83 f);
  Fuzzy(55.66);
  return 0;
}
```



