# OSI與TCP分層

![TCP&#x9023;&#x7DDA;&#x72C0;&#x614B;&#x5716;](../.gitbook/assets/tcp_connection_states.jpg)

## 埠\(Port\)的分類

從埠的性質來分，通常可以分為以下三類：

* **公認埠（Well Known Ports）**：這類埠也常稱之為常用埠。這類埠的埠號從0到1023，它們緊密繫結於一些特定的服務。
* **註冊埠（Registered Ports）**：埠號從1024到49151。它們鬆散地繫結於一些服務。也是說有許多服務繫結於這些埠，這些埠同樣用於許多其他目的。
* **動態和/或私有埠（Dynamic and/or Private Ports）**：埠號從49152到65535。

