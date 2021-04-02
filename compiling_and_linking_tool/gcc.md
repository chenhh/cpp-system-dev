# GCC

C語言編譯可分為4個步驟：

* 前置處理\(pre-processing\)
* 編譯\(compilation\)
* 組譯\(assembly\)
* 連結\(linking\)



![gcc&#x7DE8;&#x8B6F;&#x6D41;&#x7A0B;](../.gitbook/assets/gcc_compile_process.png)

![&#x7DE8;&#x8B6F;&#x6D41;&#x7A0B;&#xFF0C;&#x7531;&#x539F;&#x59CB;&#x78BC;&#x8B8A;&#x70BA;&#x6A5F;&#x5668;&#x78BC;&#xFF0C;&#x518D;&#x9023;&#x7D50;&#x5916;&#x90E8;&#x51FD;&#x5F0F;](../.gitbook/assets/compile_process.png)

## 前置處理\(preprocessing\)

預編譯過程主要處理原始碼中以 “\#” 開始的預編譯指令：

* 將所有的 “\#define” 刪除，並且展開所有的巨集定義。
* 處理所有條件預編譯指令，如 “\#if”、“\#ifdef”、“\#elif”、“\#else”、“\#endif”。
* 處理 “\#include” 預編譯指令，將被包含的檔案插入到該預編譯指令的位置。注意，該過程遞迴執行。
* 刪除所有註釋。
* 新增行號和檔名標號。
* 保留所有的 \#pragma 編譯器指令。

```text
gcc -E hello.c -o hello.i
```

前處理後的結果如下：

```c
# 1 "hello.c"
# 1 "<built-in>"
# 1 "<command-line>"
  ......
  extern int printf(const char * __restrict __format, ...);
......
main() {
  printf("hello, world\n");
}
```

## 編譯\(compilation\)

將高階語言轉換成組合語言。編譯過程就是把預處理完的檔案進行一系列詞法分析、語法分析、語義分析及優化後生成相應的組合語言程式碼檔案。

```text
gcc -S hello.c -o hello.s
```

得到的結果如下：

```c
	.file	"hello.c"
	.text
	.section	.rodata
.LC0:
	.string	"hello world"
	.text
	.globl	main
	.type	main, @function
main:
.LFB0:
	.cfi_startproc
	endbr64
	pushq	%rbp
	.cfi_def_cfa_offset 16
	.cfi_offset 6, -16
	movq	%rsp, %rbp
	.cfi_def_cfa_register 6
	leaq	.LC0(%rip), %rdi
	call	puts@PLT
	movl	$0, %eax
	popq	%rbp
	.cfi_def_cfa 7, 8
	ret
	.cfi_endproc
.LFE0:
	.size	main, .-main
	.ident	"GCC: (Ubuntu 8.4.0-3ubuntu2) 8.4.0"
	.section	.note.GNU-stack,"",@progbits
	.section	.note.gnu.property,"a"
	.align 8
	.long	 1f - 0f
	.long	 4f - 1f
	.long	 5
0:
	.string	 "GNU"
1:
	.align 8
	.long	 0xc0000002
	.long	 3f - 2f
2:
	.long	 0x3
3:
	.align 8
4:
```

## 組譯\(assembly\)

將組合語言轉換成目的檔\(或可執行檔\)。

```text
gcc -c hello.s -o hello
```

## 連結\(linking\)

將多個目的檔結合形成可執行檔。

