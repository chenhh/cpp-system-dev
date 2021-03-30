# GCC

C語言編譯可分為4個步驟：

* 前置處理\(pre-processing\)
* 編譯\(compilation\)
* 組譯\(assembly\)
* 連結\(linking\)



![gcc&#x7DE8;&#x8B6F;&#x6D41;&#x7A0B;](../.gitbook/assets/gcc_compile_process.png)

## 前置處理\(preprocessing\)

去除掉\#include、\#if等前處理的內容，可用參數`-E`得到處理過的內容。

```text
gcc -E hello.c -o hello.i
```

## 編譯\(compilation\)

將高階語言轉換成組合語言。

```text
gcc -S hello.i -o hello.s
```

## 組譯\(assembly\)

將組合語言轉換成目的檔\(或可執行檔\)。

```text
gcc -c hello.s -o hello
```

## 連結\(linking\)

將多個目的檔結合形成可執行檔。

