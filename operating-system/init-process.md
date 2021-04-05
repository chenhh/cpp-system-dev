# Init行程

## 什麼是 Init 系統

Linux 作業系統的啟動首先從 BIOS 開始，接下來進入 boot loader，由 bootloader 載入內核\(kernel\)，進行內核初始化。內核初始化的最後一步就是啟動 pid 為 1 的 init 行程。這個行程是系統的第一個行程。它負責產生其他所有用戶行程。

init 以守護行程\(daemon process\)方式存在，是所有其他行程的祖先。init 行程非常獨特，能夠完成其他行程無法完成的任務。

Init 系統能夠定義、管理和控制 init 行程的行為。它負責組織和運行許多獨立的或相關的始化工作\(因此被稱為 init 系統\)，從而讓計算機系統進入某種用戶預訂的運行模式。

僅僅將內核運行起來是毫無實際用途的，必須由 init 系統將系統代入可操作狀態。比如啟動外殼 shell 後，便有了人機交互，這樣就可以讓計算機執行一些預訂程序完成有實際意義的任務。或者啟動 X 圖形系統以便提供更佳的人機界面，更加高效的完成任務。這裡，文字界面的 shell 或者 X 系統都是一種預設的運行模式。

大多數 Linux 發行版的 init 系統是和 System V 相兼容的，被稱為 sysvinit。這是人們最熟悉的 init 系統。一些發行版如 Slackware 採用的是 BSD 風格 Init 系統，這種風格使用較少，本文不再涉及。其他的發行版如 Gentoo 是自己定製的。Ubuntu 和 RHEL 採用 upstart 替代了傳統的 sysvinit。而 Fedora 從版本 15 開始使用了一個被稱為 systemd 的新 init 系統。可以看到不同的發行版採用了不同的 init 實現。現有三個主要的 Init 系統：sysvinit，UpStart 和 systemd。

在 Linux 主要應用於服務器和 PC 機的時代，Sysvinit 運行非常良好，概念簡單清晰。它主要依賴於 Shell 腳本，這就決定了它的最大弱點：啟動太慢。在很少重新啟動的 Server 上，這個缺點並不重要。而當 Linux 被應用到移動終端設備的時候，啟動慢就成了一個大問題。為了更快地啟動，人們開始改進 sysvinit，先後出現了 upstart 和 systemd 這兩個主要的新一代 init 系統。



