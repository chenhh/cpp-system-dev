# Winsock

## 簡介

WinSock的全名是 Windows Socket，其定義為介於Windows TCP/IP application，Client 與 TCP/IP 的protocol stack之間的一項標準介面，而非協定(protocol)。

程式設計者於Windows上所寫的網路軟體，只要支援WinSock的規格，他就不需要顧慮所使用的網路卡是那家廠牌，因為Winsock所提供的程式庫Winsock.dll會負起與網路底層溝通的工作。進而讓設計者能設計出更多功能或更user friendly的網路軟體。Socket可以抽象地想成是連接網路的窗口，它提供應用程式和網路溝通的管道。Winsock API 函式提供一套 socket 介面函式，應用程式利用這套函式開啟一個 socket，透過這個 socket 和網路溝通。

現在我們使用的是Winsock2.2版，可向下相容於Winsock1.1版。應用程式要為每個開啟的 socket 命名，這樣才能名正言順地使用這些 socket 作為網路溝通的管道。我們講一個 socket 的命名，包含三個部份 :

* 通訊協定 (protocol)，通常是TCP或UDP
* IP 位址
* 埠號(Port number)

客戶端程式成功地和伺服器程式取得聯繫後，客戶端的socket和伺服器端的socket會建立起一個連結( association )。一個聯結包含五個部份 :



* 通訊協定
* 本地的IP 位址
* 本地的埠號
* 遠端IP 位址
* 遠端的埠號

伺服器端和客戶端要彼此產生通訊，一定要彼此的socket能認的對方，也就是要建立聯結的關係。能相互通訊的兩端一定是用同一種通訊協定，加上兩端socket 的IP位址，埠號和遠端socket的IP 位址，埠號，這五個部份構成了連結的關係。
