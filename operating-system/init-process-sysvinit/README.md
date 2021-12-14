# Init行程：sysvinit

## 什麼是 Init 系統

Linux 作業系統的啟動首先從 BIOS 開始，接下來進入 boot loader，由 bootloader 載入內核(kernel)，進行內核初始化。內核初始化的最後一步就是啟動 pid 為 1 的 init 行程。這個行程是系統的第一個行程。它負責產生其他所有用戶行程。

init 以守護行程(daemon process)方式存在，是所有其他行程的祖先。init 行程非常獨特，能夠完成其他行程無法完成的任務。

Init 系統能夠定義、管理和控制 init 行程的行為。它負責組織和運行許多獨立的或相關的始化工作(因此被稱為 init 系統)，從而讓計算機系統進入某種用戶預訂的運行模式。

僅僅將內核運行起來是毫無實際用途的，必須由 init 系統將系統代入可操作狀態。比如啟動外殼 shell 後，便有了人機交互，這樣就可以讓計算機執行一些預訂程序完成有實際意義的任務。或者啟動 X 圖形系統以便提供更佳的人機界面，更加高效的完成任務。這裡，文字界面的 shell 或者 X 系統都是一種預設的運行模式。

大多數 Linux 發行版的 init 系統是和 System V 相兼容的，被稱為 sysvinit。這是人們最熟悉的 init 系統。一些發行版如 Slackware 採用的是 BSD 風格 Init 系統，這種風格使用較少，本文不再涉及。其他的發行版如 Gentoo 是自己定製的。Ubuntu 和 RHEL 採用 upstart 替代了傳統的 sysvinit。而 Fedora 從版本 15 開始使用了一個被稱為 systemd 的新 init 系統。可以看到不同的發行版採用了不同的 init 實現。現有三個主要的 Init 系統：sysvinit，UpStart 和 systemd。

在 Linux 主要應用於服務器和 PC 機的時代，Sysvinit 運行非常良好，概念簡單清晰。它主要依賴於 Shell 腳本，這就決定了它的最大弱點：啟動太慢。在很少重新啟動的 Server 上，這個缺點並不重要。而當 Linux 被應用到移動終端設備的時候，啟動慢就成了一個大問題。為了更快地啟動，人們開始改進 sysvinit，先後出現了 upstart 和 systemd 這兩個主要的新一代 init 系統。

## Sysvinit 簡介

sysvinit 就是 system V 風格的 init 系統，顧名思義，它源於 System V 系列 UNIX。它提供了比 BSD 風格 init 系統更高的靈活性。是已經使用了幾十年的 UNIX init 系統，一直被各類 Linux 發行版採用。

### 運行級別(run level)

Sysvinit 用術語 run level 來定義"預訂的運行模式"。Sysvinit 檢查 `/etc/inittab` 文件中是否含有 ‘initdefault’ 項。 這告訴 init 系統是否有一個預設運行模式。如果沒有預設的運行模式，那麼用戶將進入系統控制台，手動決定進入何種運行模式。

sysvinit 中運行模式描述了系統各種預訂的運行模式。通常會有 8 種運行模式，即運行模式 0 到 6 和 S 或者 s。

每種 Linux 發行版對運行模式的定義都不太一樣。但 0、1、6 卻得到了大家的一致贊同：

* 0：關機
* 1：單用戶模式
* 6：重啟

通常在 `/etc/inittab` 文件中定義了各種運行模式的工作範圍。比如 RedHat 定義了 run level 3 和 5。運行模式 3 將系統初始化為字符界面的 shell 模式；運行模式 5 將系統初始化為 GUI 模式。無論是命令行界面還是 GUI，運行模式 3 和 5 相對於其他運行模式而言都是完整的正式的運行狀態，計算機可以完成用戶需要的任務。而模式 1，S 等往往用於系統故障之後的排錯和恢復。

很顯然，這些不同的運行模式下系統需要初始化運行的進程和需要進行的初始化準備都是不同的。比如運行模式 3 不需要啟動 X 系統。用戶只需要指定需要進入哪種模式，sysvinit 將負責執行所有該模式所必須的初始化工作。

### sysvinit 運行順序

Sysvinit 巧妙地用腳本，文件命名規則和軟鏈接來實現不同的 run level。首先，sysvinit 需要讀取`/etc/inittab` 文件。分析這個文件的內容，它獲得以下一些配置信息：

* 系統需要進入的 run level
* 捕獲組合鍵的定義
* 定義電源 fail/restore 腳本
* 啟動 getty 和虛擬控制台

得到配置信息後，sysvinit 順序地執行以下這些步驟，從而將系統初始化為預訂的 run level X：

* `/etc/rc.d/rc.sysinit`
* `/etc/rc.d/rc` 和`/etc/rc.d/rcX.d/` (X 代表運行級別 0-6)
* `/etc/rc.d/rc.local`
* X Display Manager（如果需要的話）

首先，運行 rc.sysinit 以便執行一些重要的系統初始化任務。在 RedHat 公司的 RHEL5 中(RHEL6 已經使用 upstart 了)，rc.sysinit 主要完成以下這些工作：

* 激活 udev 和 selinux
* 設置定義在`/etc/sysctl.conf` 中的內核參數
* 設置系統時鐘
* 加載 keymaps
* 使能交換分區
* 設置主機名(hostname)
* 根分區檢查和 remount
* 激活 RAID 和 LVM 設備
* 開啟磁盤配額
* 檢查並掛載所有文件系統
* 清除過期的 locks 和 PID 文件

完成了以上這些工作之後，sysvinit 開始執行`/etc/rc.d/rc` 腳本。根據不同的 run level，rc 腳本將打開對應該 runlevel 的 rcX.d 目錄(X 就是 run level)，找到並運行存放在該目錄下的所有啟動腳本。每個 run level X 都有一個這樣的目錄，目錄名為`/etc/rc.d/rcX.d`。

在這些目錄下存放著很多不同的腳本。文件名以 S 開頭的腳本就是啟動時應該運行的腳本，S 後面跟的數字定義了這些腳本的執行順序。在`/etc/rc.d/rcX.d` 目錄下的腳本其實都是一些軟鏈接文件，真實的腳本文件存放在`/etc/init.d` 目錄下。

### rc.local腳本

當所有的初始化腳本執行完畢。Sysvinit 運行`/etc/rc.d/rc.local` 腳本。

rc.local 是 Linux 留給用戶進行個性化設置的地方。您可以把自己私人想設置和啟動的東西放到這裡，一台 Linux Server 的用戶一般不止一個，所以才有這樣的考慮。

## Sysvinit 和系統關閉

Sysvinit 不僅需要負責初始化系統，還需要負責關閉系統。在系統關閉時，為了保證數據的一致性，需要小心地按順序進行結束和清理工作。比如應該先停止對文件系統有讀寫操作的服務，然後再 umount 文件系統。否則數據就會遺失。

這種順序的控制這也是依靠`/etc/rc.d/rcX.d/`目錄下所有腳本的命名規則來控制的，在該目錄下所有以 K 開頭的腳本都將在關閉系統時調用，字母 K 之後的數字定義了它們的執行順序。這些腳本負責安全地停止服務或者其他的關閉工作。

Sysvinit 的管理和控制功能


此外，在系統啟動之後，管理員還需要對已經啟動的行程進行管理和控制。原始的 sysvinit 軟體包包含了一系列的控制啟動，運行和關閉所有其他程序的工具。

* `halt`：停止系統。
* `init`：這個就是 sysvinit 本身的 init 行程實體，以 pid1 身份運行，是所有用戶行程的父進程。最主要的作用是在啟動過程中使用`/etc/inittab` 文件創建行程。
* `killall`：就是 SystemV 的 killall 命令。向除自己的會話(session)行程之外的其它行程發出信號，所以不能殺死當前使用的 shell。
* `last`：回溯`/var/log/wtmp` 文件(或者-f 選項指定的文件)，顯示自從這個文件建立以來，所有用戶的登錄情況。
* `lastb`：作用和 `last` 差不多，預設情況下使用`/var/log/btmp` 文件，顯示所有失敗登錄企圖。
* `mesg`：控制其它用戶對用戶終端的訪問。
* `pidof`：找出程序的行程識別號(pid)，輸出到標準輸出設備。
* `poweroff`：等於 `shutdown -h –p`，或者 `telinit 0`。關閉系統並切斷電源。
* `reboot`：等於 `shutdown –r` 或者 `telinit 6`。重啟系統。
* `runlevel`：讀取系統的登錄記錄文件(一般是`/var/run/utmp`)把以前和當前的系統運行級輸出到標準輸出設備。
* `shutdown`：以一種安全的方式終止系統，所有正在登錄的用戶都會收到系統將要終止通知，並且不准新的登錄。
* `sulogin`：當系統進入單用戶模式時，被 init 調用。當接收到啟動加載程序傳遞的-b 選項時，init 也會調用 sulogin。
* `telinit`：實際是 init 的一個連接，用來向 init 傳送單字元參數和信號。
* `utmpdump`：以一種用戶友好的格式向標準輸出設備顯示`/var/run/utmp` 文件的內容。
* `wall`：向所有有信息權限的登錄用戶發送消息。

不同的 Linux 發行版在這些 sysvinit 的基本工具基礎上又開發了一些輔助工具用來簡化 init 系統的管理工作。比如 RedHat 的 RHEL 在 sysvinit 的基礎上開發了 initscripts 軟件包，包含了大量的啟動腳本 (如 rc.sysinit) ，還提供了 `service`，`chkconfig` 等命令行工具，甚至一套圖形化界面來管理 init 系統。其他的 Linux 發行版也有各自的 initscript 或其他名字的 init 軟件包來簡化 sysvinit 的管理。

## Sysvinit 的小結

**Sysvinit 的優點是概念簡單**。Service 開發人員只需要編寫啟動和停止腳本，概念非常清楚；將 service 添加/刪除到某個 run level 時，只需要執行一些創建/刪除軟連接文件的基本操作；這些都不需要學習額外的知識或特殊的定義語法(UpStart 和 Systemd 都需要用戶學習新的定義系統初始化行為的語言)。

其次，**sysvinit 的另一個重要優點是確定的執行順序**：腳本嚴格按照啟動數字的大小順序執行，一個執行完畢再執行下一個，這非常有益於錯誤排查。UpStart 和 systemd 支持並發啟動，導致沒有人可以確定地瞭解具體的啟動順序，排錯不易。

但是串行地執行腳本導致 sysvinit 運行效率較慢，在新的系統環境下，啟動快慢成為一個重要問題。此外動態設備加載等 Linux 新特性也暴露出 sysvinit 設計的一些問題。針對這些問題，人們開始想辦法改進 sysvinit，以便加快啟動時間，並解決 sysvinit 自身的設計問題。





