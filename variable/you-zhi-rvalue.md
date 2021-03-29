# 右值\(rvalue\)

lvalue \(左值\)與 rvalue \(右值\)雖然最早開始似乎是代表二元運算子左右兩邊的運算元，不過在 C++ 裡面其意義已截然不同，現在要稱呼二元運算子左右兩端的運算元要用 lhs \(left hand side\) 跟 rhs \(right hand side\) 比較不會引起誤會。

非常簡略地說，能夠提取出記憶體位置的物件就是lvalue\(有名字的\(暫時\)物件\)，反之則為 rvalue\(沒有名字的\(暫時\)物件\)。

最不精確的說法是一個變數、表示式只能被寫在`=` 的右邊出現那他就是 rvalue，如果能在 = 的左右邊隨便出現的叫 lvalue。

* lvalue: 有名稱的、在記憶體中有實際位置可供程序員加以存取物件。可以使用 符號`&`取地址。
* rvalue: 沒有名稱的暫時物件，離開該敘述即被摧毀的物件。類型為T的右值引用表示為`T&&`。

lvalue 和 rvalue 是用來描述 「expressions」 的屬性而非物件。lvalue讓一段 expression 成為一個有名稱的物件；而rvalue只是一段 expression 的結果且隨時會蒸發不見。

* lvalue、rvalue 是 C++ 對運算式（expression）的分類方式，一個粗略的判別方式，是看看 & 可否對運算式取址，若可以的話，運算式是 lvalue，否則是個 rvalue。
* e.g `++x` 是lvalue，因為會回傳persistent object to x。
* 而 `x++` 是rvalue，因為回傳的是temporary copy to x。

## 左值和右值皆可以為 non-const 和 const 

```cpp
string one("cute"); // modifiable lvalue
const string two("fluffy"); // const lvalue
string three() { return "hello"; } // modfiable rvalue
const string four() { return "world"; }  // const rvalue
```

## Universal reference

```cpp
//universal reference 的宣告
template<typename T>
void gogo(T&& par); // 傳入的型別不確定是lvalue或是rvalue
 
gogo(1); // 傳入的型別為rvalue
 
int a;
int &b = a;
gogo(a); // 傳入為lvalue
gogo(b); // 傳入為reference
```

在上面範例中，`T` 是一個尚未被推斷出來的型號，但一般的 rvalue reference 中，出現在 `T` 位置的卻是一個已經明確知道的型別。

`T&&` 的行為簡單來說，就是當傳入 lvalue 時，`T` 的型別會被推導為`Foo&`，當傳入的是 rvalue 時，`T` 的型別則會是 `Foo&&`。這樣完全符合我們的預期，傳入已經存在的變數，就用reference來接，如果傳入的是暫時變數，就用 rvalue 的參考來接。

使用 universal reference 實際除了正確傳遞變數資訊外，另一個常見的用途是減少無謂的複製和物件建立的開銷。



