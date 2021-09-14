# 容器

## 常用的容器

| 名稱 | 描述 |
| :--- | :--- |
| Vec | 可變長度陣列，連續儲存資料 |
| VecDeque | 雙向佇列，適用於從頭部與尾部插入與刪除資料 |
| LinkedList | 雙向鏈結，非連續儲存資料 |
| HashMap | 以hash算法儲存key-value對 |
| BTreeMap | 以Btree算法儲存key-value對 |
| HashSet | 基於Hash算法的集合，相當於沒有值的HashMap |
| BTreeSet | 基於BTree的集合 相當於沒有值的BTreeMap |
| BinaryHeap | 基於二元樹堆積實現的優先佇列 |

## 迭代器 \(iterator\)

Rust的迭代器是指實現了Iterator trait的類型。

```rust
trait Iterator {
    type Item;
    fn next(&mut self) -> Option<Self::Item>;
    // ...
}
```

它最主要的一個方法就是`next()`，返回一個`Option<Item>`。一般情況返回`Some(Item)`；如果迭代運算完成，就返回`None`。

