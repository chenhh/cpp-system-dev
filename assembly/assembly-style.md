# 組合語言格式\(Intel, AT&T格式\)

機器語言因為電路設計，原始形式就是 010101 這種二進位形式，但也可以轉成十六進位表示。

下面這是一個 x86 機器語言指令 \(instruction\)：

* 05 0A 00 00 00 \(十六進位\)
* 用人類語言表示就是告訴某顆 x86 家族的 CPU：「把你的 `eax` 暫存器內容取出，將其跟10相加，再把結果寫回 `eax` 暫存器」。
* 用C語言表示法就是：`eax += 10;`機器語言形式顯然太麻煩了。
* 於是發明了助憶符號，比如用 `add` 代表「相加這個運算動作」；

如果綜合以上講的運算子跟運算元，寫出完整指令時，還會有一個問題。 若有 `eax`、`ecx` 兩個運算元，想要取出`eax`的值，複製到`ecx`去，到底該寫 `mov eax, ecx` 還是 `mov ecx, eax`？哪邊來源？哪邊目的？日常用的程式多以intel格式為準。而AT&T格式以gcc -S時可產生。



| C語言 | Intel style | AT&T style |
| :--- | :--- | :--- |
| 指派運算子的左側是目的地 | 靠最左邊的運算元是目的地 | 靠最右邊的運算元南結果放置處 |
| `int eax=4;` | `mov eax, 4` | `movl $4, %eax` |

編譯後的執行檔，可以用 `objdump` 去看 Intel Style 的組合語言。 如果是 gdb 偵錯則直接就有選項 disassembly-flavor intel 可以切換到Intel style組合語言。




