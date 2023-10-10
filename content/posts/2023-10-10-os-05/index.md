---
title: "[課程筆記] 作業系統概論（五）"
date: 2023-10-10T19:50:36+08:00
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

這次講解 process 的 creation & termination

# Creation of Process

![](./Screenshot%20from%202023-10-10%2019-47-35.png)

這張圖沒有表示出虛擬記憶體的概念。

所有的 parent process 建立 child process，形成整個 process tree。

在 **resource（memory） sharing** 的部份有三種可能：
1. parent 和 child 共享所有資源
2. child 共享部份 parent 資源分配
3. parent 和 child 完全獨立

在**執行**部份有兩種可能：
1. parent 和 child 的執行是 concurrent（不等待 child process）
2. parent 等待（`wait()`）child 執行完畢才繼續執行 

## 以 UNIX 為例子（第一份作業）
主要會用到兩個函數（兩個步驟）：
1. `fork()`：clone a process（產生出的 child process 和 parent process 完全一樣，整個記憶體區塊複製一份）
2. `exec()`child 呼叫 `exec()` 執行程式（雖然 process id 不變，但是記憶體會換成該程式內容，不會回來）

child 和 parent 並不共用記憶體（只是內容一樣）。
![](./Screenshot%20from%202023-10-10%2020-11-29.png)

第一個（pid = 1）process 是 `init`。
```sh
efliao@UX480FD:~$ ps aux | head
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.1  0.1 166932 11460 ?        Ss   19:38   0:02 /sbin/init splash
root           2  0.0  0.0      0     0 ?        S    19:38   0:00 [kthreadd]
root           3  0.0  0.0      0     0 ?        I<   19:38   0:00 [rcu_gp]
root           4  0.0  0.0      0     0 ?        I<   19:38   0:00 [rcu_par_gp]
root           5  0.0  0.0      0     0 ?        I<   19:38   0:00 [slub_flushwq]
root           6  0.0  0.0      0     0 ?        I<   19:38   0:00 [netns]
root           8  0.0  0.0      0     0 ?        I<   19:38   0:00 [kworker/0:0H-events_highpri]
root          10  0.0  0.0      0     0 ?        I<   19:38   0:00 [mm_percpu_wq]
root          11  0.0  0.0      0     0 ?        I    19:38   0:00 [rcu_tasks_kthread]
```




# 補充 `wait(NULL)` 去查引數！！！



# Termination of Process

分成兩種 sync/async
1. **syncronous**：child process 自己呼叫 `exit()` 把自己結束掉。其中 `exit()` 的引數會由 parent process 接收。
2. **asyncronous**：parent 要求 child process 結束（e.g., `kill` 程式）。parent（或是 grandparent）process 傳送 `SIGKILL` signal 給 child process，child process 收到 `SIGKILL` 就會到寫好的 callback function（預設就是把自己結束掉）。

