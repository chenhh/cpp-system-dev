# STL容器\(container\)比較

| 容器 | 實作資料結構 | 時間複雜度 | 有/無序 | 元素可否重複 | 其它 |
| :---: | :---: | :---: | :---: | :---: | :---: |
| [array](array.md) | 陣列 | 隨機讀寫 O\(1\) | 無序 | 可重複 | 支援隨機存取 |
| [vector](vector.md) | 陣列 | 隨機讀寫、尾部插入、尾部刪除 O\(1\)；頭部插入、頭部刪除 O\(n\) | 無序 | 可重複 | 支援隨機存取 |
| [deque](deque.md) | 雙向佇列 | 頭尾插入、頭尾刪除 O\(1\) | 無序 | 可重複 | 一個中央控制器 + 多個緩衝區，支援首尾快速增刪，支援隨機存取 |
| [forward\_list](forward_list.md) | 單向鏈結串列 | 插入、刪除 O\(1\) | 無序 | 可重複 | 不支援隨機存取 |
| [list](list.md) | 雙向鏈結串列 | 插入、刪除 O\(1\) | 無序 | 可重複 | 不支援隨機存取 |
| [stack](stack.md) | deque/list | 頂部插入、頂部刪除 O\(1\) | 無序 | 可重複 | deque 或 list 封閉頭端開口，不用 vector 的原因是容量大小有限制，擴容耗時 |
| [queue](queue-priority_queue.md) | deque/list | 尾部插入、頂部刪除 O\(1\) | 無序 | 可重複 | deque 或 list 封閉頭端開口，不用 vector 的原因是容量大小有限制，擴容耗時 |
| [priority\_queue](queue-priority_queue.md) | vector + maxheap | 頂部插入、頭部刪除 O\(1\) | 有序 | 可重複 | vector容器加上heap結構規則 |
| [set](set-multiset.md) | 紅黑樹 | 插入、刪除、查詢 O\(log\(n\)\) | 有序 | 不可重複 |  |
| [multiset](set-multiset.md) | 紅黑樹 | 插入、刪除、查詢 O\(log\(n\)\) | 有序 | 可重複 |  |
| [map](map-multimap.md) | 紅黑樹 | 插入、刪除、查詢 O\(log\(n\)\) | 有序 | 不可重複 |  |
| [multimap](map-multimap.md) | 紅黑樹 | 插入、刪除、查詢 O\(log\(n\)\) | 有序 | 可重複 |  |
| [unordered\_set](set-multiset.md) | hash table | 插入、刪除、查詢 O\(1\)；最差O\(n\) | 無序 | 不可重複 |  |
| [unordered\_multiset](set-multiset.md) | hash table | 插入、刪除、查詢 O\(1\)；最差O\(n\) | 無序 | 可重複 |  |
| [unordered\_map](map-multimap.md) | hash table | 插入、刪除、查詢 O\(1\)；最差O\(n\) | 無序 | 不可重複 |  |
| [unordered\_multimap](map-multimap.md) | hash table | 插入、刪除、查詢 O\(1\)；最差O\(n\) | 無序 | 可重複 |  |

