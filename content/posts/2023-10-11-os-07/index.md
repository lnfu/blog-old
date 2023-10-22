---
title: "[課程筆記] 作業系統概論（七）"
date: 2023-10-11T10:19:21+08:00
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

CPU scheduling

waiting time：process 等待的時間（從到了～開始執行）

- CPU burst：一段 CPU 在執行運算的時間。
- I/O burst：一段在 I/O wait 的時間。

# scheduler 在做什麼？
從記憶體中狀態是 ready 的 process 選擇一個讓 CPU 執行。

# 何時會 re-schedule？

對於 cooperative aka non-preemptive scheduling，只會有以下兩種：
1. running process -> waiting：例如呼叫 I/O
2. running process 終止

而對於 preemptive scheduling，除了上面兩種還多了另外兩種：
3. running process -> ready：例如 time sharing 的 timer IRR
4. waiting process -> ready：例如 I/O 完成

cooperative 雖然在實做上較簡單（也不用考慮到 race condition），但可能因為一些糟糕的 process（e.g., 無窮迴圈）造成系統被 process 永遠佔用 e.g., Windows 3.1。

# 衡量指表

- CPU utilization：CPU 的忙碌程度
- throughput：單位時間完成的 process 數量
- queueing time aka waiting time：在 ready queue 中的 process 等待要被執行的時間
- turnaround time：process 從開始到結束的時間（中間可能因為 I/O 之類的中斷又繼續執行）

work-consecutive：只要 ready queue 不是空的就會拿出來給 CPU 執行（不會 idle）。

把很多短的都先做，長時間的放到最後，這樣 throughput 很高，但是 waiting time 也很高。

> good average performance vs. predictable worst-case performance   

# 單處理器

## FCFS = First Come First Served
誰先來誰先做。

在 average waiting time 和 IO 的使用效率上都蠻差的，不過因為**可預測**所以還是有用。

## SJF = Shortest Job First
簡單（花的時間少）的 process 先做。

證明 SJF 是 optimal（最短的 average waiting time）。每次交換相鄰且順序是「長短」的兩個 process 成「短長」，如此就會讓 average waiting time 變小。

issue：有些要花比較多時間的可能就要一直等待


### SRJF = Shortest Remaining Job First
考慮到目前執行的 process 還**剩下**多少時間，如果有新的 process 比她還要少就 preempt。





## Round Robin


## Multi Queue

## Multi Feedback Queue





# Multiple Processor S
![](./Screenshot%20from%202023-10-11%2011-58-58.png)

## SMP
兩個 CPU 各自有 cache，透過 bus 共用一個 memory。


## NUMA
![](./Screenshot%20from%202023-10-11%2012-01-23.png)

topology 有幾種選擇：ring、mesh、**hypercube**


補充：hypercube









# 問題
FCFS 有什麼 performance issue？
1. Poor I/O utilization -> 對
2. Low CPU utilization -> 錯（基本上只要有 process，CPU 都會在使用）
3. Poor interactivity -> 對（大部分都是 I/O-bound process）




