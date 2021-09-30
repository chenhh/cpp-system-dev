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

## 建立執行緒

### 如何實現一個執行緒

使用threading模組實現一個新的執行緒，需要下面3步：

* 定義一個 Thread 類的子類；
* 重寫 `__init__(self [,args])` 方法，可以新增額外的引數；
* 最後，需要重寫 `run(self, [,args])` 方法來實現執行緒要做的事情。

當建立了新的 Thread 子類的時候，可以實例化這個類，呼叫 `start()` 方法來啟動它。執行緒啟動之後將會執行 `run()` 方法。

呼叫 `start()` 之後子執行緒變為活躍狀態，並且持續直到 `run()` 結束，或者中間出現異常。所有的執行緒都執行完成之後，程式結束。

### 以thread執行外部函數

* thread建立時，如果沒有指定`name`時，以thread-{num}命名。
* 需要被執行的函數以target在thread建立時傳入，thread會自動以成員函數run\(\)執行target函數。
* 若目標函數需要參數時，可在建立thread時，以arg關鍵字，將參數以list或tuple傳入。

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


def param_function(arg):
    t_name = threading.currentThread().getName()
    print(f"{t_name} is starting, arg={arg}")
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
    t4 = threading.Thread(name='arg_thread',
                          target=param_function,
                          args=["hello world",])
    threads = (t1, t2, t3, t4)
    # start thread
    # 建立thread時, 因為t1, t2, t3是依序建立,
    # 所以在印出starting時不會亂序
    [t.start() for t in threads]

    #  查看目前有多少個執行緒
    print(f"current acting threads: {threading.active_count()}")

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

```

### 覆寫thread class的run成員函數

* 如果不使用target在thread建構時傳入函數，也可以在覆寫thread class的run成員函數做更精細的控制。

```python
# -*- coding: UTF-8 -*-
import _thread
import threading
import time

exitFlag = 0


class MyThread(threading.Thread):
    def __init__(self, thread_id, name, counter):
        threading.Thread.__init__(self)
        self.thread_id = thread_id
        self.name = name
        self.counter = counter

    def run(self):
        print(f"{self.thread_id} starting: {self.name}")
        print_time(self.name, self.counter, 2)
        print(f"{self.thread_id} exiting {self.name}")


def print_time(thread_name, delay, counter):
    while counter:
        if exitFlag:
            # Python3中已經不能使用thread，以_thread取代
            _thread.exit()
        time.sleep(delay)
        print(f"{thread_name} {time.ctime(time.time())}")
        counter -= 1


if __name__ == '__main__':
    # Create new threads
    threads = (MyThread(1, "Thread-1", 1), MyThread(2, "Thread-2", 2))

    # Start new Threads
    [t.start() for t in threads]

    # wait child threads complete
    [t.join() for t in threads]
    print("Exiting Main Thread")
```

### setDaemon

若子執行緒希望在主執行緒執行完畢後，不管其他的執行緒是否已執行完畢，都強制跟主執行緒一起結束，則必須寫在start\(\) 之前，預設為 False。

```python
# -*- coding: UTF-8 -*-
import threading
import time


def thread_first_job(x):
    name = threading.currentThread().getName()
    time.sleep(5)
    print(f"thread name:{name}, 1st job: {x}")
    print(f"thread name:{name}, 1st job finished")


def thread_second_job(x):
    name = threading.currentThread().getName()
    print(f"thread name:{name}, 2nd job: {x}")
    print(f"thread name:{name}, 2nd job finished")


if __name__ == '__main__':
    t1 = threading.Thread(target=thread_first_job, args=("Hi",))
    t2 = threading.Thread(target=thread_second_job, args=("Hello",))
    # t2的daemon設為true, 則不論t1是否結束, t2會和main thread一起結束,
    # 必須寫在t2.start之前
    t2.setDaemon(True)
    t1.start()
    t2.start()
    print("main thread finished")

    """
    thread name:Thread-2, 2nd job: Hello
    thread name:Thread-2, 2nd job finished
    main thread finished
    thread name:Thread-1, 1st job: Hi
    thread name:Thread-1, 1st job finished
    """
```

## Queue

[https://docs.python.org/zh-tw/3.8/library/queue.html](https://docs.python.org/zh-tw/3.8/library/queue.html)

如果只是threading之間要通訊，可以使用queue。Thread 無法回傳值，所以要使用 `Queue.put()` 將要傳回的值存入 Queue，再用 `Queue.get()` 取出。

Queue是同步的類別，實現了多生產者、多消費者隊列。這特別適用於訊息必須安全地在多線程間交換的線程編程。模塊中的 Queue 類實現了所有所需的鎖定語義。

Queue模組有幾個類別：

* queue.Queue：FIFO佇列。
* queue.LifoQueue：LIFO 隊列構造函數
* queue.PriorityQueue：優先級隊列構造函數。
* queue.SimpleQueue：無界的 FIFO 隊列構造函數。簡單的隊列，缺少任務跟蹤等高級功能。

而Python的threading模組提供了很多同步方法，包括互斥量\(mutex\)、訊號量\(semaphore\)、原子變數\(atomic\)、條件變數\(conditional variable\)、事件\(event\)和鎖\(lock\)。如果可以使用這些方法的話，應該優先考慮使用這些，而不是使用queue（佇列）模組。

## 參考資料

* [\[python\] thread module](https://docs.python.org/zh-tw/3.8/library/threading.html)

