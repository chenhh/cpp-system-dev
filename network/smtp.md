# SMTP

每一個使用者都會有一個信箱(mail box)來存放尚未讀取的信件。尚未傳送出去的信件會暫時存在message queue。

SMTP (Simple Mail Transfer Protocol)扮演了兩種角色：

* Client：當作傳送信件的伺服器
* Server：當作接收信件的伺服器
* Client和server這兩種服務都會執行在每一台郵件伺服器上



* RFC編號: 821。
* 使用TCP作為下層溝通的協定，port 25。

傳送三部曲，所有信息資訊必須是7-bit ASCII。

* Handshaking
* Transfer of messages
* closure

## 訊息格式

RFC編號：822，text message exchange標準。

![](../.gitbook/assets/smtp\_message-min.png)




MIME (multimedia mail extension)
--------------------------------

RFC編號：2045, 2046。

MIME為了非ASCII資料的擴充。在表頭檔定義了MIME的格式。

![MIME定義在表頭](../.gitbook/assets/MIME\_extension-min.png)

MIME所增加的標頭檔(headers)

* MIME-Version：定義MIME的版本。
* Content-Description：描述訊息內容。
* Content-ID：唯一的識別碼。
* Content-Transfer-Encoding：傳送資料時所使用的編碼，如下表。

| 編碼名稱             | 說明                                           |
| ---------------- | -------------------------------------------- |
| 7bit             | 資料全部以ASCII呈現。                                |
| 8bit             | 資料部份不為ASCII。                                 |
| binary           | 資料部份不為ASCII。                                 |
| quoted-printable | 將資料編碼成人類可讀的符號。                               |
| base64           | 通過將6位的輸入塊映射到8位的輸出塊來編碼資料，所有這些數據都是可列印的ascii字符。 |
| x-token          | 非標準編碼的名稱。                                    |



* Content-Type：訊息的種類，常用種類如下表。

| 類別          | 子類別           | 說明                                                      |
| ----------- | ------------- | ------------------------------------------------------- |
| text        | plain         | 未格式的文字，ASCII文字或是ISO 8859。                               |
| multipart   | mixed         | 不同的部分是獨立的，但要一起傳輸。它們應按郵件信息中出現的順序向收件人提交。                  |
|             | parallel      | 與mixed的區別僅在於沒有規定向接收方交付部件的順序。                            |
|             | alternative   | 不同的部分是同一信息的替代版本。它們的排列順序是越來越忠實於原始信息，接收者的郵件系統應該向用戶顯示最佳版本。 |
|             | digest        | 類似於mixed，但預設的類別/子類別是message/rfc822。                     |
| message     | rfc822        | 本身就是一個符合RFC822的封裝信息。                                    |
|             | partial       | 用於允許大郵件的碎片化，以一種對收件人透明的方式。                               |
|             | external-body | 包含指向一個存在於其他地方的對象的指標。                                    |
| image       | jpeg          | 影像為jpeg格式                                               |
|             | gif           | 影像為gif格式                                                |
| video       | mpeg          | 影片為mpeg格式                                               |
| audio       | basic         | 8-bit單頻道, 8kHz的音訊。                                      |
| application | octet-stream  | 一般的二進位資料                                                |



