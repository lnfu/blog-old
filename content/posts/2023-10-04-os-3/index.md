---
title: "[課程筆記] 作業系統概論（三）"
date: 2023-10-04T10:21:45+08:00
draft: false
author: "Enfu Liao"
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


