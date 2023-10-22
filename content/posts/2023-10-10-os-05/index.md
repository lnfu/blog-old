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

注意，一般來說為了不要太慢，實際上 `fork()` 並不會馬上複製一份相同的記憶體區塊，而是使用 CPU 的 MMU，來達到映射（mapping），不過在複製時就會 copy on write 才不會改到 parent process。

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

### 結論
`fork()` 複製 process。

`exec()` 用某個程式把整個 process 的記憶體取代掉。


# 補充 `wait(NULL)` 去查引數！！！



# Termination of Process

分成兩種 sync/async
1. **syncronous**：child process 自己呼叫 `exit()` 把自己結束掉。其中 `exit()` 的引數會由 parent process 接收。
2. **asyncronous**：parent 要求 child process 結束（e.g., `kill` 程式）。parent（或是 grandparent）process 傳送 `SIGKILL` signal 給 child process，child process 收到 `SIGKILL` 就會到寫好的 callback function（預設就是把自己結束掉）。


## Zombie Process

如果 child process 已經結束（resource 都釋放完畢）但是 `exit()` 的引數沒有被 parent process 拿走，這個 child process 就會變成 zombie process（aka defunct process）。

此時 child process 明明已經結束卻會在 process table 占用一個 entry。

在第一個程式作業中，要如何使背景執行的 child process 不要變成 zombie process 有兩種作法：
1. double fork（aka detach）
2. 用 signal 通知 child process 已經結束

## Orphan Process

在 child process 還沒結束之前 parent process 已經結束了。

以 Linux 來說，orphan process 會被 `init` process 給領養走。

# `vfork()`

前面有提到，使用 `fork()` 並不會馬上複製整份記憶體區塊，而是使用映射的方式達到類似效果，不過這需要使用到 CPU 的 MMU。

有些低階的裝置 CPU 並不支援 MMU，所以無法使用這樣的模式，然而直接複製整份又不實際（速度太慢，尤其是低階 CPU 又更慢了），這時候可以使用 `vfork()` 函數。

呼叫 `vfork()` 時會先讓 parent process 暫停，並且讓 child process 直接繼承使用 parent process 的 stack（function、local variable），直到 child process 呼叫 `exec()` 才會真正去複製整份的記憶體。

不過使用 `vfork()` 就會去改到全域變數。



# IPC = Interprocess Communication

兩種 model。
![](./Screenshot%20from%202023-10-04%2010-23-26.png)

## Shared Memory
- `shmget()`：建立一塊 share memory
- `shmat()`：attach 到目前 process 的記憶體地址空間
- `shmdt()`：detach
- `chmctl()`：控制 share memory（e.g., 刪除）

## Message Passing
producer 放東西到 buffer（write）

consumer 從 buffer 拿東西出來（read）

一種實現方式：bounded-buffer

![](./Screenshot%20from%202023-10-04%2010-27-39.png)


```c
// insert()
while (true) {
    /* Produce an item */
    while (( (in + 1) % BUFFER SIZE count) == out)
        ; /* do nothing -- no free buffers */

    buffer[in] = item;
    in = (in + 1) % BUFFER SIZE;
}
```

```
// remove()
while (true) {
    while (in == out)
        ; // do nothing -- nothing to consume

    // remove an item from the buffer
    item = buffer[out];
    out = (out + 1) % BUFFER SIZE;
    return item;
}
```

以上是一個（效能）糟糕的設計，因為會造成迴圈一直空轉浪費 CPU 資源（直到 time sharing 的時間下來）。

Why not to use a free-slot counter 用變數紀錄現在 buffer 有幾個元素？第六章會講到這會造成 race（錯誤）。



**2 operations：**
1. send(message) -> write
2. receive(message) -> read

以 C 語言來說就是 `pipe()`。

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/types.h>
int main(void)
{
    int fd[2], nbytes;
    pid_t childpid;
    char string[] = "Hello, world!\n";
    char readbuffer[80];
    pipe(fd); // create the pipe before calling fork()
    if((childpid = fork()) == -1)
    {
        perror("fork");
        exit(1);
    }
    if(childpid == 0)
    {
        /* Child process closes up input side of pipe */
        close(fd[0]);
        /* Send "string" through the output side of pipe */
        write(fd[1], string, (strlen(string)+1));
        exit(0);
    }
    else
    {
        /* Parent process closes up output side of pipe */
        close(fd[1]);
        /* Read in a string from the pipe */
        nbytes = read(fd[0], readbuffer, sizeof(readbuffer));
        printf("Received string: %s", readbuffer);
    }
    return 0;
}
```

`dup2()`

## Signal

UNIX 特有，可以想成 interrupt 之於 CPU 等同 signal 之於 process。

根據 manual，請使用 `sigaction()` 不要用 `signal()`。

`SIGSEGV`：memory protection fault
`SIGFPE`
`SIGKILL`：殺掉 process
`SIGSTOP`：suspend process
`SIGCHLD`：


