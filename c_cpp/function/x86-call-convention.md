# x86呼叫約定(cdecl, stdcal)

## x86系統呼叫約定

### 核心介面

#### Linux 32-bit系統

Linux 系統呼叫使用暫存器傳遞引數。`eax` 為 syscall\_number，`ebx`、`ecx`、`edx`、`esi`、`ebp` 用於將 6 個引數傳遞給系統呼叫。返回值儲存在 `eax` 中。所有其他暫存器（包括 EFLAGS）都保留在 `int 0x80` 中。

#### &#xD;Linux 64-bit系統

核心介面使用的暫存器有：`rdi`、`rsi`、`rdx`、`r10`、`r8`、`r9`。系統呼叫通過 `syscall` 指令完成。除了 `rcx`、`r11` 和 `rax`，其他的暫存器都被保留。系統呼叫的編號必須在暫存器 `rax` 中傳遞。系統呼叫的引數限制為 6 個，不直接從堆疊上傳遞任何引數。返回時，`rax` 中包含了系統呼叫的結果。而且只有 INTEGER 或者 MEMORY 型別的值才會被傳遞給核心。

### 使用者介面

#### Linux 32-bit系統

引數通過堆疊進行傳遞。最後一個引數第一個被放入堆疊中，直到所有的引數都放置完畢，然後執行 `call` 指令。這也是 Linux 上 C 語言函式的方式。

#### Linux 64-bit系統

x86-64 下通過暫存器傳遞引數，這樣做比通過堆疊有更高的效率。它避免了記憶體中引數的存取和額外的指令。根據引數型別的不同，會使用暫存器或傳參方式。如果引數的型別是 MEMORY，則在堆疊上傳遞引數。如果型別是 INTEGER，則順序使用 `rdi`、`rsi`、`rdx`、`rcx`、`r8` 和 `r9`。所以如果有多於 6 個的 INTEGER 引數，則後面的引數在堆疊上傳遞。
