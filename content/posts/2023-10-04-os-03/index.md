---
title: "[課程筆記] 作業系統概論（三） - 行程（Process）"
date: 2023-10-04T10:21:45+08:00
draft: false
author: "Enfu Liao"
tags: ["課程筆記", "作業系統"]

# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

# Process（行程）
process = job = task = a program **in execution**
- text
- stack：local variable
- heap：全域變數
- data
- program counter, CPU registers

![process in memory](./Screenshot%20from%202023-09-27%2011-57-56.png)


# Process 的狀態（State）：ready、running、waiting
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

![uC/OS-2 context switch push](./Screenshot%20from%202023-10-29%2002-12-29.png)
![uC/OS-2 context switch pop](./Screenshot%20from%202023-10-29%2002-13-34.png)

步驟：
1. push (current) status register 
2. push (current) general purpose register
3. save (current) stack pointer
4. load (next) stack pointer
5. pop (next) general purpose register
6. pop (next) status register

- PSW: Processor Status Word
- OSTCBCur: TCB of the current task
- OSTCBHighRdy: TCB of the new task

# 參考

- https://www.csie.ntu.edu.tw/~ktw/rts/uCOSII-prn.pdf