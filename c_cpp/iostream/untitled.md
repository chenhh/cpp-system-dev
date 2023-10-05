# printf

C語言標準中，定義了以下格式化輸出的函數：

```c
#include <stdio.h>

int printf(const char *format, ...);
int fprintf(FILE *stream, const char *format, ...);
int dprintf(int fd, const char *format, ...);
int sprintf(char *str, const char *format, ...);
int snprintf(char *str, size_t size, const char *format, ...);

#include <stdarg.h>

int vprintf(const char *format, va_list ap);
int vfprintf(FILE *stream, const char *format, va_list ap);
int vdprintf(int fd, const char *format, va_list ap);
int vsprintf(char *str, const char *format, va_list ap);
int vsnprintf(char *str, size_t size, const char *format, va_list ap);
```

* `fprintf()` 按照格式字串的內容將輸出寫入流中。三個引數為流、格式字串和變參列表。&#x20;
* `printf()` 等同於 `fprintf()`，但是它假定輸出流為 stdout。&#x20;
* `sprintf()` 等同於 `fprintf()`，但是輸出不是寫入流而是寫入陣列。在寫入的字串末尾必須新增一個空字元。&#x20;
* `snprintf()` 等同於 `sprintf()`，但是它指定了可寫入字元的最大值 size。當 size 大於零時，輸出字元超過第 size-1 的部分會被捨棄而不會寫入陣列中，在寫入陣列的字串末尾會新增一個空字元。&#x20;
* `dprintf()` 等同於 `fprintf()`，但是它輸出不是流而是一個檔案描述符 fd。&#x20;
* `vfprintf()`、`vprintf()`、`vsprintf()`、`vsnprintf()`、`vdprintf()` 分別與上面的函式對應，只是它們將變參列表換成了 va\_list 型別的引數。



函式原型：  `int printf ( const char * format, ... );`



引數說明： `%[flags][width][.precision][length]specifier`



## 資料型態 ( %\[旗標]\[寬度]\[.精度]\[長度修飾]資料型態 ) 必填欄位

### 字元/字串

| 型態       | 說明                               |
| -------- | -------------------------------- |
| `%c, %C` | 字元，`char c;`                     |
| `%s`     | C-style字元陣列， `char str[LENGTH];` |
| `%S`     | 寬字元陣列，`wchar str[LENGTH];`       |

`%C / %S` 並未被收在標準函式庫裡，屬 MSVC 特殊支援。
