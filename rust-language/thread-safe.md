---
description: 線程
---

# 執行緒安全

## 簡介

Rust不僅在沒有自動垃圾回收（Garbage Collection）的條件下實現了記憶體安全，而且實現了執行緒安全。Rust編譯器可以在編譯階段避免所有的資料競爭（Data Race）問題。這也是Rust的核心競爭力之一。

## 執行緒\(thread\)

執行緒是作業系統能夠進行調度的最小單位，它是行程 \(process\)中的實際運作單位，每個滿程至少包含一個執行緒。

在多核處理器越來越普及的今天，多執行緒程式設計也用得越來越廣泛。多執行緒的優勢有：

* 容易利用多核優勢；
* 比單執行緒反應更敏捷，比多進程資源分享更容易。

多執行緒程式設計在許多領域是不可或缺的。但是，多執行緒並行，非常容易引發資料競爭，而且還非常不容易被發現和debug。

Rust標準庫中與執行緒相關的內容在[std::thread](https://doc.rust-lang.org/std/thread/index.html)模組中。Rust中的執行緒是對作業系統執行緒的直接封裝。

```rust
use std::thread;
use std::time::Duration;
fn main() {
    // 使用Builder模式為child thread指定更多的訊息
    let t = thread::Builder::new()
        .name("child1".to_string())
        .spawn(
            // 新建的thread
            move || {
            println!("enter child thread.");
            thread::park();    // 進入等待狀態
            println!("resume child thread");
        })
        .unwrap();
    println!("spawn a thread");
    thread::sleep(Duration::new(5, 0));
    t.thread().unpark();    // 恢復等待的狀態
    // parent等待child thread結束
    t.join();
    println!("child thread finished");
}
/*
spawn a thread
enter child thread.
resume child thread
child thread finished
*/
```

## 避免資料競爭

```rust
use std::thread;
fn main() {
    let mut health = 12;
    // spawn函數接受的參數是一個閉包。
    // 我們在閉包裡面引用了函數體內的區域變數，
    // 而這個閉包是執行在另外一個執行緒上，
    // 編譯器無法肯定區域變數health的生命週期一定大於閉包的生命週期，
    // 於是發生了錯誤。
    // 解法：move
    thread::spawn(move || {
        health *= 2;
    });
    // 12, 值並未改變
    // ？因為health是Copy類型，在遇到move型閉包的時候，
    // 閉包內的health實際上是一份新的複製，外面的變數並沒有被真正修改。
    println!("{}", health); 
}
```

那我們究竟怎樣做才能讓一個變數在不同執行緒中共用呢？**答案是：我們沒有辦法在多執行緒中直接讀寫普通的共用變數，除非使用Rust提供的執行緒安全相關的設施**。

也就是說，Rust給我們提供了一個重要的安全保證：The compiler prevents all data races。**資料競爭，意思是在多執行緒程式中，不同執行緒在沒有使用同步的條件下並行訪問同一塊資料，且其中至少有一個是寫操作的情況**。

在許多傳統（非函數式）的程式設計語言中，並行程式設計是非常困難的，原因就在於程式碼中存在大量的共用狀態和很多隱藏的資料依賴。程式設計師必須非常清楚程式碼的流程，使用合適的策略正確實現併發控制。而萬一某人在某個地方犯了一個小錯誤，那麼這個程式就成了不安全的，而且沒有什麼靜態檢查工具可以保證完整無遺漏地將此類問題檢查出來。

根據資料競爭的定義，它的發生需要三個條件：

* 資料共用：有多個執行緒同時訪問一份資料；
* 資料修改：至少存在一個執行緒對資料做修改；
* 沒有同步：至少存在一個執行緒對資料的訪問沒有使用同步措施。

**我們只要讓這三個條件無法同時發生即可**。

* 可以禁止資料共用，比如actor-based concurrency，多執行緒之間的通信僅靠發送消息來實現，而不是通過共用資料來實現；
* 可以禁止資料修改，比如functional programming，許多函數式程式設計語言嚴格限制了資料的可變性，而對共用性沒有限制。

然而以上設計在許多情況下都有一些性能上的缺陷，無法達到“零開銷抽象”的目的。

傳統C/C++需要依賴程式師不犯錯誤來保證執行緒安全；而Rust是由工具自動保證的。









