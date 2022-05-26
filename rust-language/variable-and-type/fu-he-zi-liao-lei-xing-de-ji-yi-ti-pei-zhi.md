# 複合資料類型的記憶體配置

## 尺寸和對齊量(size and alignment)

值的對齊量(alignment of a value)指定了哪些位址可以有效地存儲該值。對齊量為 n 的值只能存儲位址為 n 的倍數的內存位址上。

例如，對齊量為 2 的值必須存儲在偶數位址上，而對齊量為 1 的值可以存儲在任何位址上。對齊量是用位元組來度量的，必須至少是 1，並且總是 2 的冪次。值的對齊量可以通過函數 `align_of_val` 來檢測。

* [\[crate\] std::mem::align\_of\_val](std::mem::align\_of\_val)

值的尺寸(size of a value)是同類型的值組成的數組中連續兩個元素之間的位元組偏移量，此偏移量包括了為保持程式項類型內部對齊而對此類型做的對齊填充。值的尺寸總是其對齊量的非負整數倍數。值的尺寸可以通過函數 `size_of_val` 來檢測。

* [\[crate\] std::mem::size\_of\_val](https://doc.rust-lang.org/std/mem/fn.size\_of\_val.html)

如果某類型的所有的值都具有相同的尺寸和對齊量，並且兩者在編譯時都是已知的，並且實現了 Sized trait，則可以使用函數 size\_of 和 align\_of 對此類型進行檢測。<mark style="color:blue;">沒有實現 Sized trait 的類型被稱為動態(dynamic size type)尺寸類型</mark>。由於實現了 Sized trait 的某一類型的所有值共享相同的尺寸和對齊量，所以我們分別將這倆共享值稱為該類型的尺寸和該類型的對齊量。

## 原生類型的佈局

[\[crate\] std::mem::size\_of](https://doc.rust-lang.org/std/mem/fn.size\_of.html)

```rust
use std::mem;

fn main() {
    // Some primitives
    assert_eq!(4, mem::size_of::<i32>());
    assert_eq!(8, mem::size_of::<f64>());
    assert_eq!(0, mem::size_of::<()>());

    // Some arrays
    assert_eq!(8, mem::size_of::<[i32; 2]>());
    assert_eq!(12, mem::size_of::<[i32; 3]>());
    assert_eq!(0, mem::size_of::<[i32; 0]>());

    // Pointer size equality
    assert_eq!(mem::size_of::<&i32>(), mem::size_of::<*const i32>());
    assert_eq!(mem::size_of::<&i32>(), mem::size_of::<Box<i32>>());
    assert_eq!(mem::size_of::<&i32>(), mem::size_of::<Option<&i32>>());
    assert_eq!(
        mem::size_of::<Box<i32>>(),
        mem::size_of::<Option<Box<i32>>>()
    );
}
```

## 參考資料

* [\[rust refernence\] Type Layout](https://doc.rust-lang.org/reference/type-layout.html)
