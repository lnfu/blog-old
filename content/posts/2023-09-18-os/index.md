---
title: "[課程筆記] 作業系統概論"
date: 2023-09-18T00:50:52+08:00
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

課程簡介：略

## 電腦開機
1. PC = FFFF:0000 執行 BIOS（現在電腦通常改成 UEFI）
2. 磁碟最開頭的 512 Bytes 叫做 MBR（負責尋找磁碟上的作業系統 e.g., Windows）


## Interrupt handling（中斷處理）

CPU 一次只能做一件事，如果有一個事情卡住，就可以透過中斷來去做別的事情。

分成：
* 硬體中斷：e.g., IO
* 軟體中斷：e.g., 除以 0

這兩種中斷基本上處理的流程一樣。

### 範例流程

![中斷處理範例流程圖](./Screenshot%20from%202023-09-18%2001-18-00.png)

1. IO device 發出中斷訊號給 PIC
2. PIC 發中斷給 CPU
3. CPU 把目前在處理的東西（當前狀態 e.g., program counter, ...）先存起來
4. CPU 查詢 IVT（interrupt vector table） 後跳到對應的 ISR（interrupt service routine） 執行
5. ISR 最後一個指令會是 IRET（return 回原本 CPU 處理的東西）

> IVT（中斷向量表）。位置固定（方便 CPU 查詢），通常放在記憶體位置為 0。

> 作業系統（or 驅動程式）要負責 ISR 的部份。

![中斷處理時間線](./typical-interrupt-timeline-l.jpg)

> 不要忘記會有延遲（latency ）

## I/O
一個 IO 基本上最花時間的部份是：
1. 在 IO device 處理的時間
2. IO device 傳到 memory 的 data transfer 時間

DMA 負責從 IO device（e.g., 磁碟）到 buffer in memory 的高速傳輸。（傳完送 inturrupt 給 CPU）

### Sync（blocking） vs. Async（non-blocking） I/O

| Function   | sync or async? | 說明                                                         |
|------------|----------------|--------------------------------------------------------------|
| read()     | sync           | 進行讀的 process 會需要 wait，CPU 會在這時去跑其他的 process。 |
| write()    | async          |                                                              |
| fsync()    | sync           | system call。可以看成 sync 版本的 write。                      |
| aio_read() | async          | async 的 read，用來做作 prefetch。                             |

![讀寫比較](./Screenshot%20from%202023-09-18%2001-39-32.png)

> system call：kernel 提供的服務


## multiprogramming

process = job = task

為什麼要很多的 process？
像是前面提到屬於 sync 的 read()，如果 CPU 執行時遇到就會 blocking，此時若能利用這個 wait 的時間去執行其他的 process 就能節省時間。

### timesharing
除了前面所說 blocking 切換 process，經過一段固定的時間（透過 timer）自動切換到下一個 process。
如此一來可以達到像是均勻分配的效果。

time slice = time quantum

### 結論：timesharing ⊂ multitasking ⊂ multiprogramming 
* multiprogramming：載入很多 process 到 memory
* multitasking：multiprogramming 有 overlapped
* timesharing：要有 periodic switch


