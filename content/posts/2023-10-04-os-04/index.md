---
title: "[課程筆記] 作業系統概論（四）"
date: 2023-10-04T11:24:31+08:00
draft: false
author: "Enfu Liao"
tags: ["課程筆記"]
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---


這章講 thread。

![](./Screenshot%20from%202023-10-04%2011-26-13.png)

a process is a **container** of all its thread.

CPU 在**排程（scheduling）**時是以 kernel thread 為單位。（資源分配以 process 為單位）

同一個 process 的 thread 共用 code, data, files。但是會有自己的 registers ,stack。

# Pthread = POSIX thread
Pthread 指的是規格不是實做。（所以 Windows 也可以有）


新增 thread：
```c
#include <pthread.h>

int pthread_create(pthread_t *thread, const pthread_attr_t *attr,
                    void *(*start_routine) (void *), void *arg);
```

`start_routine` 另一個 thread 是進入點。（原本的 thread 繼續往下）


等待 thread（像是 `fork()` 的 `wait()`）：

```c
#include <pthread.h>

int pthread_join(pthread_t thread, void **retval);
```




# 三種 Model

pthread 沒有規定是要哪種 model。（把那些 api 實做出來就算）

## Many-to-One Model
一次只有一個 thread 能 access kernel，所以即使你有很多 CPU 也沒辦法 multithread 平行。
![](./Screenshot%20from%202023-10-04%2011-51-24.png)

## One-to-One
user-level thread 一對一 kernel-level thread
![](./Screenshot%20from%202023-10-04%2011-52-46.png)

## Many-to-Many
如果像是下面有兩個 kernel thread 被用掉，那麼其餘 user thread 就會共用剩下的那一個。

![](./Screenshot%20from%202023-10-04%2011-53-22.png)


# 問題
Consider a multithreaded process consisting of 2 IO-bound threads and 4 CPU-bound threads. Let the threading model be M:M. Let there be 5 kernel threads.

At most how many CPU cores that the multithreaded process can fully utilize?

3 個。
因為兩個 IO-bound 用掉兩個 thread，剩下四個共用三個。