---
title: "[課程筆記] 作業系統概論（四）"
date: 2023-10-04T11:24:31+08:00
draft: false
author: "Enfu Liao"
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

