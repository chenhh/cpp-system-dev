---
description: 線程
---

# 執行緒\(thread\)

## 簡介

執行緒是獨立的處理流程，可以和系統的其他執行緒並行或並發地執行。**多執行緒可以共享資料和資源**，利用所謂的共享記憶體空間。

執行緒和行程的具體實現取決於所執行的作業系統，但是總體來講，我們可以說執行緒是包含在行程中的，同一行程的多個不同的執行緒可以共享相同的資源。相比而言，行程之間不會共享資源。

* 每一個執行緒基本上包含3個元素：程式計數器，暫存器和變數堆疊。
* 每一個執行緒也有自己的執行狀態，可以和其他執行緒同步，這點和程序一樣。執行緒的狀態大體上可以分為ready, running, blocked。

Python中，\_thread是低階的執行緒模組，threading是高階的執行緒模組。

在 CPython 中，由於存在全域性解釋器鎖\(GIL\)，同一時刻只有一個執滿者可以執行 Python 代碼。 如果想讓應用更好地利用多核心計算機的計算資源，推薦你使用 multiprocessing 或 concurrent.futures.ProcessPoolExecutor。 但是，如果你想要同時運行多個 I/O 密集型任務，則多執行緒仍然是一個合適的模型。

## 建立thread

```python
# -*- coding: UTF-8 -*-
import threading
import time


def first_function():
    t_name = threading.currentThread().getName()
    print(f"{t_name} is starting")
    time.sleep(2)
    print(f"{t_name} is exiting")
    return


def second_function():
    t_name = threading.currentThread().getName()
    print(f"{t_name} is starting")
    time.sleep(2)
    print(f"{t_name} is exiting")
    return


def third_function():
    t_name = threading.currentThread().getName()
    print(f"{t_name} is starting")
    time.sleep(2)
    print(f"{t_name} is exiting")
    return


if __name__ == "__main__":
    # 建立Thread object
    # target 是用於 run() 方法調用的可調用對象。
    t1 = threading.Thread(name='1st_thread',
                          target=first_function)
    t2 = threading.Thread(name='2nd_thread',
                          target=second_function)
    t3 = threading.Thread(name='3rd_thread',
                          target=third_function)
    threads = (t1, t2, t3)
    # start thread
    # 建立thread時, 因為t1, t2, t3是依序建立,
    # 所以在印出starting時不會亂序
    [t.start() for t in threads]

    # wait thread finish
    # thread完成時，不一定會按照次序，所以exiting會亂序
    # 如果沒有join時，main thread會直接結束而不會
    # 等到child threads結束
    [t.join() for t in threads]
    print("finished")

# 1st_thread is starting
# 2nd_thread is starting
# 3rd_thread is starting
# 2nd_thread is exiting
# 1st_thread is exiting
# 3rd_thread is exiting
# finished
# 如果沒有join時，child threads starting後
# main thread會先印出finished, child threads
# 才會印出exiting
```



## 參考資料

* [\[python\] thread module](https://docs.python.org/zh-tw/3.8/library/threading.html)

