# 結構\(struct\)

## C與C++結構的比較

| C | C++ |
| :--- | :--- |
| 結構內只有單純的資料，沒有成員函數與存取權限 | 結構內可以有成員函數與存取權限，與class的差異只有在於結構內的存取權限預設為public |
| 必須使用struct關鍵字與struct\_name才能宣告變數。 | 只須用strcuct\_name即可宣告變數。 |
| struct內不可有static member | 可以有static member |
| 對於空結構，sizeof之值為0 | 對於空結構，sizeof之值為1 |

