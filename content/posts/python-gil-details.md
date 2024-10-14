+++
title = 'Python 的 GIL 是什麼？'
description = '簡單來說，GIL 是一個 mutex，用來避免在 multi-threading 的情況下發生 race condition 並確保 thread safety'
slug = 'python-gil-details'
tags = ['Python']
date = 2021-07-11T23:51:20+08:00
draft = false
+++

## 前言

在面試的時候被問到 GIL，雖然我是直接回答說我看不懂，但是仔細想想上一次看也是快一年前了，於是想趁報到前再來挑戰一次 GIL

主要會分兩個部分來講

1. GIL 是什麼
2. GIL 會造成什麼影響
3. 如何避免 GIL 造成的問題
4. 總結

---

## GIL 是什麼？

> In CPython, the global interpreter lock, or GIL, is a mutex that protects access to Python objects, preventing multiple threads from executing Python bytecodes at once. The GIL prevents race conditions and ensures thread safety. A nice explanation of [how the Python GIL helps in these areas can be found here](https://python.land/python-concurrency/the-python-gil). In short, this mutex is necessary mainly because CPython’s memory management is not thread-safe.

簡單來說，GIL 是一個 mutex，用來避免在 multi-threading 的情況下發生 race condition 並確保 thread safety

來源：[https://wiki.python.org/moin/GlobalInterpreterLock](https://wiki.python.org/moin/GlobalInterpreterLock)

這邊又出現了三個名詞：

- mutex
- race condition
- thread safe

接著先讓我們搞懂這三個東西是什麼再接著說下去

因為其他兩個名詞都跟 race condition 有關，所以在講其他兩個之前我們得先了解 race condition 是什麼

### Race Condition

在 multi-threading 的情況下，每個 thread 的資源是共享的，所以不同的 thread 可以同時讀寫同一個變數，這樣就會造成程式輸出的結果有可能會因為不同 thread 執行的先後而導致輸出完全不一樣，例如：

我們現在用兩個 thread 跑同樣的程式 `count++`，假設兩個 thread 分別是 t1、t2，執行程式的過程都是一樣，要經過下面這兩步

1. 拿到 `count` 的值
2. 把 `count` 的值 +1 後存回去

當執行順序是這樣的話：

> `count` 預設值為 0

1. t1 拿 `count` 值 -> `count = 0`
2. t2 拿 `count` 值 -> `count = 0`
3. t1 把 `count + 1` (0 + 1 = 1) 存回去 -> `count = 1`
4. t2 把 `count + 1` (0 + 1 = 1) 存回去 -> `count = 1`

我們原本的預期行為是 `count = 2`，但是因為變數共用再加上這樣的執行順序就有可能導致 `count = 1`，這樣的狀況我們就稱為 **race condition**

### Mutex

Mutex 就是一種避免發生 race condition 的方式，它的做法就是把共用的區塊加上一把鎖，在有其中一個 thread 正在讀寫這個區塊時，其他的 thread 就不能讀寫這個區塊。加上鎖後，上面的狀況就會變成這樣：

1. t1 拿 `count` 值，並把 `count` 鎖起來 -> `count = 0`
2. t2 嘗試拿 `count` 值，但 `count` 被鎖了，所以拿不到
3. t1 把 `count + 1` (0 + 1 = 1) 存回去，並解鎖 `count` -> `count = 1`
4. （因為 `count` 的鎖被解開了）t2 拿 `count` 值 -> `count = 1`
5. t2 把 `count + 1` (1 + 1 = 2) 存回去 -> `count = 2`

這樣的值就跟我們預期中的一樣了

### Thread Safety

可以簡單理解成，不會發生 race condition 的狀況就是 **thread safety**

---

## GIL 會造成什麼影響

了解 GIL 是什麼後，我們就可以來看一下 GIL 造成了什麼影響

我們要先分成 I/O bound 跟 CPU bound 兩種不同類型的程式來看

1. **I/O bound**

   在 I/O bound 的程式下，只要執行 blocking 的 system call，系統就會釋放 CPU 資源給其他 thread 使用，所以 GIL 對 I/O bound 的影響並不大

2. **CPU bound**

   CPU bound 這邊要再分成單核跟多核的狀況來看

   - **單核**

     不同的 thread 輪流使用 CPU 資源，整體上除了跑起來從原本預期的 parallel 變成 concurrent 之外沒什麼太大的影響

   - **多核**

     如果兩個執行 CPU bound 任務的 thread 在不同的核心上執行，可能會出現以下的情況：其中一個核心上的 thread 在拿到 GIL 後執行任務時，另一個核心上的 thread 不停地搶 GIL 失敗，於是該核心長期處於空轉的狀態

---

## 如何避免 GIL 造成的問題

GIL 只有在 CPython 上有，所以可以換其他的 interpreter，如：PyPy、Jython。如果在不能換掉 CPython 的狀況下，可以改用 multi-processing 來執行 CPU bound 的任務

---

## 總結

1. GIL 的作用是避免產生 race condition 並確保 thread safety
2. GIL 對 CPU bound 的任務影響比較大
3. 可以透過換其他的 interpreter 或是改用 multi-processing 來避免 GIL 產生的效能問題

---

## 參考資料

- [Python的GIL是什么鬼，多线程性能究竟如何](http://cenalulu.github.io/python/gil-in-python/)
- [Python3 原始碼閱讀-深入瞭解Python GIL](https://iter01.com/510417.html)
- [深入 GIL: 如何寫出快速且 thread-safe 的 Python – Grok the GIL: How to write fast and thread-safe Python](https://blog.louie.lu/2017/05/19/深入-gil-如何寫出快速且-thread-safe-的-python-grok-the-gil-how-to-write-fast-and-thread-safe-python/)
