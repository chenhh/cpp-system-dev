# Windows API

## 簡介

WinAPI（也稱為 Win32;官方稱為 Microsoft Windows API）是 Microsoft 用 C 編寫的應用程式程式設計介面，允許訪問 Windows 功能。Win32API是Windows 10之前所用的API，而現在的Windows 10 則是用另一種API。

Windows API提供了幾個主要功能：

* 基礎服務(Base Services) : 提供介面，使Windows系統裡的基礎資源可以存取。
* 圖形裝置介面(GUI) : 輸出圖形的內容到外部輸出裝置。
* 圖形化使用著介面(GUI) : 建立和管理螢幕及大多數控制項。
* 通用對話方塊連結媒體櫃(Common Dialog Library) : 是應用程式提供標準的對話方塊，使介面不會太混亂。
* 通用控制線連結媒體櫃(Common Control Library) : 應用程式提供介面，來存取一些作業系統提供的進階控制項。
* Windows外殼(Windows Shell) : 允許應用程式存取Windows外殼所提供的功能。
* 網路服務(Network Services) : 存取作業系統提供的多種網路功能介面。

Windows API裡也有Web相關的API。Web API是Web的服務器或是瀏覽器的應用程式介面。是以Web開發的概念去做，通常是給Web應用程式的客戶端使用，所以多少會有些限制。

另外，Windows API也有提供多媒體相關的API，如DirectX是一組提供相關多媒體圖像及遊戲的服務，也就是Microsoft 以DirectX API作為安裝Windows的一部份。

最後，Windows API 還提供剩下兩個相關的API及多媒體用的。

* 程式通訊API：從一開始的Dynamic Data Exchange，簡稱為DDE，是用來協調作業系統裡的應用程式的資料叫喚及呼叫命令；到物件連結及嵌入，是讓應用程式建立包含不同來源的複合文件；到最後的元件物件模型，縮寫為COM(Component Object Model)。
* 封裝媒體櫃：是Microsoft用許多低層的Windows API，來開發出許多的封裝媒體庫，使應用程式可以更方便地與Windows API做互動。而我們常看到的大多數Windows Application framework是給Windows API做封裝用的。

## 參考資料

* [\[MSDN\] Programming reference for the Win32 API](https://docs.microsoft.com/en-us/windows/win32/api/)
* [\[MSDN\] 使用 WIN32 API 建立桌面 Windows 應用程式](https://docs.microsoft.com/zh-tw/windows/win32/)
* [Win32 API教程](http://www.tastones.com/zh-tw/stackoverflow/win32-api/)
