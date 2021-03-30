# unique\_ptr

## modern C++的智慧指標

早在 C++11 推出前，C++ 就有 `std::auto_ptr` 可用。但是，該組件有潛在的問題，因此不建議使用，而替代品就是`std::unique_ptr`。

建構指標的`make_unique`函數在C++14才放在&lt;memory&gt;中，C++11沒有的原因委員會忘記放進去了。

unique的意義是指確保一份資源（被配置出來的記憶體空間）只會被一個 `unique_ptr` 物件管理的智慧指標；當 `unique_ptr` 指向物件消失時\(程式正常結束或丟出異常\)，指標會自動釋放資源。即使函數內部發生例外，也能保證該物件被正確刪除，並釋放佔用的記憶體。

Modern C++中不再使用原始指標，且不再使用 `new/delete`，全面改用智慧指標及輔具來管理物件。

**要不要刪物件，要怎麼刪物件都「不用管」，而且很安全**。

`unique_ptr` 是在 &lt;memory&gt;  標準程式庫的標頭中定義。 它與原始指標的效率完全相同，而且可以在 C++ 標準程式庫容器中使用。 將實例新增 `unique_ptr` 至 C++ 標準程式庫容器會很有效率，因為的移動函式不需要複製操作。

### std::make\_unique

建立並傳回指向指定型別之物件的 unique\_ptr，該型別是使用指定的引數所建構。

```cpp
// 第一個多載用於單一物件。 
// make_unique<T>
template <class T, class... Args>
unique_ptr<T> make_unique(Args&&... args);

// 針對陣列叫用第二個多載。
// make_unique<T[]>
template <class T>
unique_ptr<T> make_unique(size_t size);

// 第三個多載可防止您在型別引數 (make_unique) 中
// 指定陣列大小 <T[N]> ; 目前的標準並不支援此結構。
// make_unique<T[N]> disallowed
template <class T, class... Args>
/* unspecified */ make_unique(Args&&...) = delete;
```

## 物件所有權轉移

`unique_ptr`不會共用其指標。 它無法複製到另一個 `unique_ptr` ，以傳值方式傳遞至函式，或是用於需要進行複製的任何 c + + 標準程式庫演演算法。 只能移動 `unique_ptr`。

 這表示記憶體資源的擁有權轉移到另一個 `unique_ptr`，原始 `unique_ptr` 不再擁有它。 因為多重擁有權會增加程式邏輯的複雜度。 因此，當您需要純 C + + 物件的智慧型指標時，請使用 `unique_ptr` ，而且當您建立時 `unique_ptr` ，請使用 `make_unique` 函式。

![unique\_ptr&#x5FC5;&#x9808;&#x7528;std::move&#x8F49;&#x79FB;&#x6240;&#x6709;&#x6B0A;](../../.gitbook/assets/unique_ptr.png)

```cpp
#include <cstdio>
#include <memory>

struct Point {
  int x, y;
};

int main() {
  // std::unique_ptr
  std::unique_ptr < Point > ptr = 
  std::make_unique < Point > ();
  ptr -> x = 2;
  ptr -> y = 1;
  printf("(x,y)=(%d, %d)\n", ptr -> x, ptr -> y);

  // by moving 'ptr' to 'ptr2', 
  // ptr轉移所有權後，改為指向nullptr
  std::unique_ptr < Point > ptr2 = std::move(ptr);
  printf("(x,y)=(%d, %d)\n", ptr2 -> x, ptr2 -> y);

  return 0;
}
```

## unique\_ptr與container一起使用

容器在將物件存入時，是使用copy ctor，因此若物件較大時，會消耗許多計算資源。其中一種解決方式是將指向物件的指標存入容器中，然可解決上述的問題，但使用者必須自行管理容器中指標的生命週期，仍然相當不便。

若在容器中使用unique\_ptr，則可由程式自動管理指標的生命容期。

```cpp
#include <cstdio>
#include <memory>
#include <string>
#include <vector>
using namespace std;

struct Song{
    Song(){}
    Song(string singer_, string name_):
        singer(singer_), name(name_){}
    string singer, name;
};

int main(){
    // Song obj("hello", "world");
    //vector內存的是unique_ptr<T>的類別，而非原始的指標
    vector<unique_ptr<Song>> songs;
    songs.push_back(make_unique<Song>("Bz", "Juice"));
    songs.push_back(make_unique<Song>("Namie Amuro", "Funky Town"));
    songs.push_back(make_unique<Song>("Kome Kome Club", "Kimi ga Iru Dake de"));
    songs.push_back(make_unique<Song>("Ayumi Hamasaki", "Poker Face"));

    for (const auto& song : songs){
        printf("singer: %s, title:%s \n",
            song->singer.c_str(), song->name.c_str());
    } 

    // Create a unique_ptr to an array
    unique_ptr<Song[]> ptr = make_unique<Song[]>(4);   
}

```

## 

## 參考資料

* [\[MSDN\] 作法：建立和使用 unique\_ptr 執行個體](https://docs.microsoft.com/zh-tw/cpp/cpp/how-to-create-and-use-unique-ptr-instances?view=msvc-160&viewFallbackFrom=vs-2019)



