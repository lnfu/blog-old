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



