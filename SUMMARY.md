# Table of contents

* [簡介](README.md)

## 組合語言 <a id="assembly"></a>

* [x86 CPU架構簡介](assembly/x86-cpu-arch.md)
* [x86定址模式\(addressing mode\)](assembly/x86-addressing-mode.md)
* [組譯器\(assembler\)](assembly/assembler.md)
* [目的檔\(object file\)](assembly/object-file/README.md)
  * [Windows PE格式](assembly/object-file/pe-format.md)
* [程式重定址\(program relocation\)](assembly/program-relocation.md)
* [載入程式\(loading program\)](assembly/loading-program.md)
* [連結器\(linker\)](assembly/linker/README.md)
  * [靜態連結\(static linking\)](assembly/linker/static-linking.md)
* [暫存器\(register\)](assembly/register.md)
* [保護模式\(protect mode\)](assembly/protect-mode/README.md)
  * [記憶體保護機制](assembly/protect-mode/mem-protection-mechanism.md)
  * [中斷與例外處理](assembly/protect-mode/interrupt-and-exception.md)
* [大端與小端\(big endian and little endian\)](assembly/big-endian-and-little-endian.md)
* [組合語言格式\(Intel, AT&T格式\)](assembly/assembly-style.md)

## C/C++變數宣告 <a id="variable"></a>

* [有號數與無號數\(signed and unsigned number\)](variable/signed-unsigned-integer.md)
* [列表初始化\(list initialization\)](variable/list-initialization.md)
* [指標\(pointer\)](variable/pointer/README.md)
  * [unique\_ptr](variable/pointer/unique_ptr-1.md)
  * [shared\_ptr](variable/pointer/shared_ptr.md)
  * [unique\_ptr](variable/pointer/unique_ptr.md)
* [reference](variable/reference.md)
* [const](variable/const.md)
* [volatile](variable/volatile.md)
* [陣列\(array\)](variable/array.md)
* [結構\(struct\)](variable/struct.md)
* [右值\(rvalue\)](variable/rvalue/README.md)
  * [std::move](variable/rvalue/std-move.md)
  * [perfect forwarding](variable/rvalue/perfect-forwarding.md)
* [static](variable/static.md)
* [enum](variable/enum.md)
* [聯合\(union\)](variable/union.md)

## C/C++ 分支條件 <a id="branch-condition"></a>

* [if-else](branch-condition/if-else.md)

## C/C++ 迴圈 <a id="loop"></a>

* [for](loop/for.md)
* [while](loop/while.md)

## C/C++ 函數 <a id="function"></a>

* [function](function/function.md)
* [lambda function](function/lambda-function.md)
* [過載\(overloading\)](function/overloading.md)
* [function pointer](function/function-pointer.md)
* [x86呼叫約定\(cdecl, stdcal\)](function/x86-call-convention.md)

## C++ Exception <a id="cpp-exception"></a>

## C/C++ IO <a id="c-cpp-io"></a>

* [iostream](c-cpp-io/iostream.md)
* [printf](c-cpp-io/untitled.md)
* [stringstream](c-cpp-io/stringstream.md)
* [fstream](c-cpp-io/fstream.md)

## C++ class

* [建構子與解構子\(constructor and destructor\)](c++-class/constructor-and-destructor.md)
* [override關鍵字](c++-class/override.md)
* [final關鍵字](c++-class/final.md)
* [virtual](c++-class/virtual.md)
* [default與delete關鍵字](c++-class/default-and-delete.md)
* [RAII](c++-class/raii.md)

## C++ template

* [template function](c++-template/template-function.md)

## C++ STL

* [STL主要元件](c++-stl/stl-main-component.md)
* [STL容器\(container\)](c++-stl/stl-container/README.md)
  * [array](c++-stl/stl-container/array.md)
  * [vector](c++-stl/stl-container/vector.md)
  * [deque](c++-stl/stl-container/deque.md)
  * [forward\_list](c++-stl/stl-container/forward_list.md)
  * [list](c++-stl/stl-container/list.md)
  * [stack](c++-stl/stl-container/stack.md)
  * [queue, priority\_queue](c++-stl/stl-container/queue-priority_queue.md)
  * [set, mutliset, unordered version](c++-stl/stl-container/set-multiset.md)
  * [map, multimap, unordered version](c++-stl/stl-container/map-multimap.md)
  * [bitset](c++-stl/stl-container/bitset.md)
  * [string\_view](c++-stl/stl-container/string_view.md)
  * [string](c++-stl/stl-container/string.md)
* [STL迭代器\(iterator\)](c++-stl/stl-iterator.md)
* [STL仿函式\(Functor\)](c++-stl/stl-functor.md)
* [STL演算法\(Algorithm\)](c++-stl/stl-algorithm.md)
* [filesystem](c++-stl/filesystem.md)
* [thread](c++-stl/thread.md)

## 編譯與連結工具 <a id="compiling_and_linking_tool"></a>

* [GNU binutils](compiling_and_linking_tool/tool.md)
* [GCC](compiling_and_linking_tool/gcc.md)
* [CMake](compiling_and_linking_tool/cmake.md)
* [LLVM](compiling_and_linking_tool/llvm.md)

## 作業系統 <a id="operating-system"></a>

* [開機流程](operating-system/computer-start.md)
* [記憶體管理\(memory management\)](operating-system/memory-management/README.md)
  * [分頁\(paging\)](operating-system/memory-management/fen-ye-paging.md)
  * [虛擬記憶體\(virtual memory\)](operating-system/memory-management/virtual-memory.md)
  * [記憶體置換\(memory swapping\)](operating-system/memory-management/ji-yi-ti-zhi-huan-memory-swapping.md)
  * [連續記憶體分配\(contiguous memory allocation\)](operating-system/memory-management/contiguous-memory-allocation.md)
* [保護環\(protection rings\)](operating-system/protection-rings.md)
* [NUMA架構](operating-system/numa.md)
* [CPU快取一致性協議MESI](operating-system/cpu-kuai-qu-yi-zhi-xing-xie-yi-mesi.md)
* [中斷\(interrupt\)](operating-system/interrupt.md)
* [系統呼叫\(system call\)](operating-system/xi-tong-hu-jiao-system-call.md)
* [Windows API](operating-system/windows-api.md)
* [GLIBC](operating-system/glibc.md)
* [IO](operating-system/io.md)
* [Init行程：sysvinit](operating-system/init-process-sysvinit/README.md)
  * [init行程：upstart](operating-system/init-process-sysvinit/init-process-upstart.md)
  * [init行程：systemd](operating-system/init-process-sysvinit/init-process-systemd.md)

---

* [檔案系統\(file system\)](file-system.md)
* [NOR與NAND Flash](nor-and-nand-flash.md)

## 網路 <a id="network"></a>

* [OSI與TCP分層](network/osi_tcp_layers.md)
* [Internet protocol](network/ip.md)
* [資料鏈結層\(data link layer\)](network/data-link-layer.md)
* [BSD socket](network/bsd-socket.md)
* [Winsock](network/winsock.md)
* [HTTP](network/http.md)
* [SMTP](network/smtp.md)
* [DNS](network/dns.md)
* [websocket](network/websocket.md)

## 逆向工程 <a id="reverse-engineering"></a>

* [逆向工程簡介](reverse-engineering/ni-xiang-gong-cheng-jian-jie.md)
* [Ghidra](reverse-engineering/ghidra.md)
* [Radare2](reverse-engineering/radare2.md)
* [加殼與脫殼](reverse-engineering/shelling.md)
* [RSA](reverse-engineering/rsa.md)
* [網路安全簡介](reverse-engineering/basic-network-security.md)

## 平行處理 <a id="parallel-processing"></a>

* [平行處理簡介](parallel-processing/parallel-processing-introduction.md)
* [平行計算簡介](parallel-processing/basic-parallel-computing.md)

## 韌體 <a id="firmware"></a>

* [BIOS](firmware/bios.md)
* [UEFI](firmware/uefi.md)
* [EDK2](firmware/edk2.md)

## rust語言 <a id="rust-language"></a>

* [Rust簡介](rust-language/introduction.md)
* [crate與mod](rust-language/crate-and-mod.md)
* [變數與類型](rust-language/variable-and-type/README.md)
  * [基本資料類型](rust-language/variable-and-type/basic-data-type.md)
  * [複合資料類型](rust-language/variable-and-type/compounded-data-type.md)
  * [陣列](rust-language/variable-and-type/array.md)
  * [字串](rust-language/variable-and-type/string.md)
* [語句與運算式](rust-language/statement-and-expression.md)
* [模式解構\(pattern destructure\)](rust-language/pattern-destructure.md)
* [函數](rust-language/function.md)
* [trait](rust-language/trait.md)
* [巨集](rust-language/macro.md)

