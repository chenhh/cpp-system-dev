# printf

函式原型：  `int printf ( const char * format, ... );` 



引數說明： `%[flags][width][.precision][length]specifier`



## 資料型態 \( %\[旗標\]\[寬度\]\[.精度\]\[長度修飾\]資料型態 \) 必填欄位

### 字元/字串

| 型態 | 說明 |
| :--- | :--- |
| `%c, %C` | 字元，`char c;` |
| `%s` | C-style字元陣列， `char str[LENGTH];` |
| `%S` | 寬字元陣列，`wchar str[LENGTH];` |

`%C / %S` 並未被收在標準函式庫裡，屬 MSVC 特殊支援。

