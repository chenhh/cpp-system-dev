# perfect forwarding

有的時候，我們需要將一個函式某一組引數原封不動地傳遞給另一個函式。這裡不僅需要引數的值不變，而且需要引數的型別屬性\(左值/右值，cv qualifiers\)保持不變，這叫做 Perfect Forwarding（完美轉發）。

