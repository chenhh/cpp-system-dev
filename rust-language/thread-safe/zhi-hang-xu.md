# 執行緒

## 建立執行緒

```rust
use std::thread;
use std::time::Duration;
// main and child threads會交互出現
fn main() {
    // child spawn thread
    thread::spawn(|| {
        for i in 1..10 {
            println!("{i} from the spawned thread!");
            // thread::sleep 調用強制線程停止執行一小段時間，這會允許其他不同的線程運行
            thread::sleep(Duration::from_millis(2));
        }
    });

    // main thread
    for i in 1..5 {
        println!("{i} from the main thread!");
        thread::sleep(Duration::from_millis(1));
    }
    // 有可能在main thread結束前，child thread還沒執行結束
    println!("all threads complete");
}
```

## 使用 join 等待所有執行緒結束

可以通過將 thread::spawn 的返回值儲存在變量中來修復新建線程部分沒有執行或者完全沒有執行的問題。thread::spawn 的返回值類型是 JoinHandle。JoinHandle 是一個擁有所有權的值，當對其調用 join 方法時，它會等待其線程結束。

```rust
use std::thread;
use std::time::Duration;

fn main() {
    let handle = thread::spawn(|| {
        for i in 1..10 {
            println!("hi number {i} from the spawned thread!");
            thread::sleep(Duration::from_millis(2));
        }
    });

    for i in 1..5 {
        println!("hi number {i} from the main thread!");
        thread::sleep(Duration::from_millis(1));
    }

    // 確保在main結束前，child thread都結束了
    handle.join().unwrap();
    println!("all threads complete");
}
```
