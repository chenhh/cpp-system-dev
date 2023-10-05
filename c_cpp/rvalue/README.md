# 右值(rvalue)

lvalue (左值)與 rvalue (右值)雖然最早開始似乎是代表二元運算子左右兩邊的運算元，不過在 C++ 裡面其意義已截然不同，現在要稱呼二元運算子左右兩端的運算元要用 lhs (left hand side) 跟 rhs (right hand side) 比較不會引起誤會。

非常簡略地說，能夠提取出記憶體位置的物件就是lvalue(有名字的(暫時)物件)，反之則為 rvalue(沒有名字的(暫時)物件)。

最不精確的說法是一個變數、表示式只能被寫在`=` 的右邊出現那他就是 rvalue，如果能在 = 的左右邊隨便出現的叫 lvalue。

* lvalue: 有名稱的、在記憶體中有實際位置可供程序員加以存取物件。可以使用 符號`&`取地址。
* rvalue: 沒有名稱的暫時物件，離開該敘述即被摧毀的物件。類型為T的右值引用表示為`T&&`。

lvalue 和 rvalue 是用來描述 「expressions」 的屬性而非物件。lvalue讓一段 expression 成為一個有名稱的物件；而rvalue只是一段 expression 的結果且隨時會蒸發不見。

* lvalue、rvalue 是 C++ 對運算式（expression）的分類方式，一個粗略的判別方式，是看看 & 可否對運算式取址，若可以的話，運算式是 lvalue，否則是個 rvalue。

```cpp
int i = 2;
int a = 0, b = 1;
int * p = new int(24);
std::string str1 = "nice";
std::string str2 = "Scala";
std::vector < int > vec;
vec.push_back(24);
const int & m = 666;
```

| 表示式                  | 左值或右值                                      |
| -------------------- | ------------------------------------------ |
| `4`                  | rvalue                                     |
| `i`                  | lvalue                                     |
| `a+b`                | rvalue                                     |
| `&a`                 | rvalue                                     |
| `*p`                 | lvalue                                     |
| `++i`                | lvalue(字首自增運算子直接在原變數上自增)                   |
| `i++`                | rvalue(字尾自增運算子先拷貝一份變數，自增後再重新賦值給原變數)        |
| `std::string("oye")` | rvalue                                     |
| `str1+str2`          | rvalue(過載的+運算子返回的是一個臨時的std::string物件而不是引用) |
| `vec[0]`             | lvalue(過載的\[]運算子返回型別為int&)                 |
| `m`                  | lvalue(引用了一個右值，但本身是左值)                     |

## 左值和右值皆可以為 non-const 和 const&#x20;

```cpp
string one("cute"); // modifiable lvalue
const string two("fluffy"); // const lvalue
string three() { return "hello"; } // modfiable rvalue
const string four() { return "world"; }  // const rvalue
```

## Universal reference

並不是所有型別為`T&&`皆代表右值引用，`T&&`有可能代表universal reference或者rvalue reference。`T&&`為universal reference必須滿足以下兩個條件：

* 型別為`T&&`。&#x20;
* `T`參與型別推導過程。(右值引用不需型別推導，因為在宣告時已知型別)。

&#x20;C++11 的型別推導對於兩個 `&` 的規定其實很簡單明瞭：傳進來的參數列達式如果是一個 lvalue，然後表示式的型別是 `E`，那麼 `T` 就會變成 `E&`；如果表示式是 rvalue，且型別一樣為 `E` ，那麼 `T` 就會被推斷成 `E&&`。我們看到 `T&&` 可以接 lvalue reference，也可以接上 rvalue reference，這也是他被稱作 universal reference 的由來。

那麼如何確定universal reference的引用型別呢？**有如下規則：如果universal reference是通過rvalue初始化的，那麼它就對應一個rvalue reference；如果universal reference是通過lvalue初始化的，那麼它就對應一個lvalue reference**。

```cpp
//universal reference 的宣告
template<typename T>
void gogo(T&& par); // 傳入的型別不確定是lvalue或是rvalue
 
gogo(1); // T推斷成int&&
 
int a;        // lvalue
int &b = a;   // lvalue
gogo(a); // T推斷成int&
gogo(b); // T推斷成int&
```

在上面範例中，`T` 是一個尚未被推斷出來的型號，但一般的 rvalue reference 中，出現在 `T` 位置的卻是一個已經明確知道的型別。

`T&&` 的行為簡單來說，就是當傳入 lvalue 時，`T` 的型別會被推導為`Foo&`，當傳入的是 rvalue 時，`T` 的型別則會是 `Foo&&`。這樣完全符合我們的預期，傳入已經存在的變數，就用reference來接，如果傳入的是暫時變數，就用 rvalue 的參考來接。

使用 universal reference 實際除了正確傳遞變數資訊外，另一個常見的用途是減少無謂的複製和物件建立的開銷。

**Universal reference接受到的參數，如果再次使用時，會變成左值，必須靠std::forward才可維持為右值**。



### Reference collapsing&#x20;

推斷完型別之後，接下來是繼續完成函式具現化（template instantiation)。所以我們就依照推斷出來的 `T` 來具現化一下整個函式。

* `gogo(1)` 具現化出來的函式原型是 `void gogo(int&& &&par)`經過collapsing得 `void gogo(int &&par)`。
* `gogo(a)`與`gogo(b)` 具現化出來的函式原型是 `void gogo(int& &&par)`經過collapsing得到`void gogo(int &par)`。

對於這種經過型別推導之後，在具現化出現三顆四顆 & 的情況。C++11 有一套規定來簡化他們：reference collapsing。經由推斷型別後總共有四種組合形成這種情況 (不限定在 template type deduction)：

* `int& &`
* `int&& &`
* `int& &&`
* `int&& &&`
* 簡單規則：凡是有左值引用參與，均為左值引用，否則為右值引用。

reference collapsing 規則規定只有第四種會化簡成 rvalue reference `(int &&)`，其它三種都變成 lvalue reference。

為什麼需要 reference collasping，因為 C++11中的perfect forwarding需要此特性。

## 問題

### universal reference的`T&&`如果換成`const T&&` 的狀況?

如果沒有const修飾字時，為universal reference，若有CV-qualifier時，則不是unversal reference。

```cpp
template <typename T>
void gogo(T&& par) {
}	// T&&為universal reference

template <typename T>
void gogo(const T&& par) {
}	// const T&&不是universal reference

template <typename T>
class TClass {
  public:
	// par未參與型別推斷，因為T在class初始化時就決定了，
	// 不為universal reference。
    void gogo(T&& par) {
    }
}
```

* class沒有實作move constructor時，如果建立實體時使用`std::move`會發生什麼事?
* 編譯器會自動產生default (copy) constructor，何時會有default move constructor而不必使用者自行實作?



## 參考資料

* [Universal References in C++11 -- Scott Meyers](https://isocpp.org/blog/2012/11/universal-references-in-c11-scott-meyers)

### &#xD;

