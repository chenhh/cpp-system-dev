# 測試

## 簡介

Rust 中的測試函數是用來驗證非測試代碼是否按照期望的方式運行的。測試函數體通常執行如下三種操作：

* 設置任何所需的數據或狀態。
* 運行需要測試的代碼。
* 斷言(assert)其結果是我們所期望的。

測試有三種風格：

* 單元測試。&#x20;
* 文檔測試。&#x20;
* 整合測試。

有時僅在測試中才需要一些依賴（比如基準測試相關的）。這種依賴要寫在 Cargo.toml 的 \[dev-dependencies] 部分。這些依賴不會傳播給其他依賴於這個包的包。

## 如何撰寫測試？

加上`#[cfg(test)]`的模組在執行`cargo test`指令的時候其底下有被加上`#[test]`的函數會被執行。

Rust的「測試」就是一個函數，這個函數被用來驗證其它非測試函數的函數(沒有加上#\[test]的函數)所實作的功能。

測試函數的主體，通常會有以下三個行為：

1. &#x20;建構測試時需要的資料和狀態。
2. 執行我們要測試的程式(例如要被測試的函數)。
3. 驗證程式執行之後的結果是不是跟我們預期的一樣。

## 單元測試

大多數單元測試都會被放到一個叫 `tests` 的、帶有 `#[cfg(test)]` 屬性的模塊(mod)中，測試函數要加上 `#[test]` 屬性。完成後，可以使用 cargo test 來運行測試。

```rust
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

// 這個加法函式寫得很差，本例中我們會使它失敗。
#[allow(dead_code)]
fn bad_add(a: i32, b: i32) -> i32 {
    a - b
}

#[cfg(test)]
mod tests {
    // 注意這個慣用法：在 tests 模組中，從外部作用域匯入所有名字。
    use super::*;

    #[test]
    fn test_add() {
        assert_eq!(add(1, 2), 3);
    }

    #[test]
    fn test_bad_add() {
        // 這個斷言會導致測試失敗。注意私有的函式也可以被測試！
        assert_eq!(bad_add(1, 2), 3);
    }
}
```

### 測試 panic

一些函數應當在特定條件下 panic。為測試這種行為，可使用 `#[should_panic]` 屬性。這 個屬性接受可選參數 expected = 以指定 `panic` 時的訊息。如果你的函數能以多種方式 `panic`，這個屬性就保證了你在測試的確實是所指定的 `panic`。

```rust
pub fn divide_non_zero_result(a: u32, b: u32) -> u32 {
    if b == 0 {
        panic!("Divide-by-zero error");
    } else if a < b {
        panic!("Divide result is zero");
    }
    a / b
}

#[cfg(test)]
mod tests {
    // 注意這個慣用法：在 tests 模組中，從外部作用域匯入所有名字。
    use super::*;

     #[test]
    fn test_divide() {
        assert_eq!(divide_non_zero_result(10, 2), 5);
    }

    #[test]
    #[should_panic]
    fn test_any_panic() {
        divide_non_zero_result(1, 0);
    }

    #[test]
    #[should_panic(expected = "Divide result is zero")]
    fn test_specific_panic() {
        divide_non_zero_result(1, 10);
    }
}
```

### 執行特定的測試

要執行特定的測試，只要把測試名稱傳給 `cargo test` 命令就可以了。如上範例 `cargo test test_any_panic`。

要運行多個測試，可以僅指定測試名稱中的一部分，用它來匹配所有要運行的測試。如`cargo test panic`。

### 忽略測試

可以把屬性 `#[ignore]` 賦予測試以排除某些測試，或者使用 `cargo test -- --ignored` 命令來運行它們。

```rust
 #[test]
    #[ignore]
    fn ignored_test() {
        assert_eq!(add(0, 0), 0);
    }
```

## 測試的組織結構

Rust 傾向於根據測試的兩個主要分類來考慮問題：單元測試（unit tests）與 整合測試（integration tests）。

* 單元測試傾向於更小而更集中，在隔離的環境中一次測試一個模塊，或者是測試私有介面。
* 而整合測試對於你的庫來說則完全是外部的。它們與其他外部代碼一樣，通過相同的方式使用你的代碼，只測試公有介面而且每個測試都有可能會測試多個模塊。
