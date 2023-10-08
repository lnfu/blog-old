---
title: "[課程筆記] 作業系統概論（三）"
date: 2023-10-04T10:21:45+08:00
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

這章講 process。

# 待補
快去看影片！

# Process State
![](./Screenshot%20from%202023-10-05%2015-11-16.png)

假設只有一個 CPU，running 只會有一個， ready 和 waiting（例如讀檔時等待 I/O）都可能有多個。

之所以要分開 ready 和 waiting，是因為假設混在一起，CPU 就可能選到明明 I/O （假設是 waiting I/O）還沒用好的 process。

注意！沒有 ready 到 waiting 和 waiting 到 running 的狀態轉移。

running process 有兩種結果：
1. waiting：自願
2. ready：非自願
    - 因為 time sharing 所以產生 interrupt
    - 某個已經 ready high priority process preempt 當前 running process

# 問題
以下哪些 transitions 是由 hardware interrupt 觸發的？
![待補圖片]()


# PCB = Process(or Task) Control Block

儲存：
- process state
- CPU registers 裡面的當前值
- scheduling info（e.g., priority）
- memory info（第八章會講）
- I/O info（e.g., 這個 process 開啟了哪些檔案）


# Context Switch

context switch 要花費時間（overhead），所以不能太常切換，但也不能太少（否則 time sharing 效率不好）。

context switch 不只有 time sharing，一般 running process 結束也會要 context switch。

除了 overhead 以外，還有 cache pollution 的問題。

## 以 uC/OS-2 為例
![uC/OS-2 圖片]()

待補流程...




# Interprocess Communication

![](./Screenshot%20from%202023-10-04%2010-23-26.png)

## Shared Memory
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


## Message Passing

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


