# 基本型態

## C語言資料型態

只有`long`與`pointer`在不同環境下長度有異。

| 資料型態 | 一般32-bit環境 | 一般64-bit環境 | x86-64 |
| :--- | :--- | :--- | :--- |
| char | 1 | 1 | 1 |
| short | 2 | 2 | 2 |
| int | 4 | 4 | 4 |
| **long** | 4 | 8 | 8 |
| float | 4 | 4 | 4 |
| double | 8 | 8 | 8 |
| **pointer** | 4 | 8 | 8 |

## 整數的編碼

無號數\(unsigned number\)：`X`為二進位的編碼，`U(X)`為對應的十進位，`N`為編碼的長度\(4-byte時N=32，以此類推\)。

$$
U(X)=\sum_{i=0}^{N-1} {x_i} \cdot {2^i}
$$

有號數\(signed number\)：MSB\(most significant bit\)之值為0時是正數，為1時是負數，以2的補數得十進位。

$$
U(X)=-x_{N-1} \cdot 2^{N-1} \sum_{i=1}^{N-2} x_i \cdot 2^{i}
$$

| 變數 | 十進位\(dec\) | 十六進位\(hex\) | 二進位\(binary\) |
| :--- | :--- | :--- | :--- |
| `x` | 15213 | 3B 6Dh | 00111011 01101101 |
| `y` | -15213 | C4 93h | 11000100 10010011 |

### short, int, long, long long

### signed and unsigned type

## 浮點數

### float and double

## 布林值

## null, nullptr

## auto

