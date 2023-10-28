---
title: "[課程筆記] 作業系統概論（七） - 排程（Scheduling）"
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

waiting time：process 等待的時間（從到了～開始執行）

- CPU burst：一段 CPU 在執行運算的時間。
- I/O burst：一段在 I/O wait 的時間。

# scheduler 在做什麼？
從記憶體中選擇一個狀態是 ready 的行程給 CPU 執行。

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

# 單處理器（軟體排程）

## 先到先服務（First Come First Served；FCFS）
用一個先進先出（FIFO）的佇列（queue），誰先來誰先做。

缺點：
- 平均等待時間（average wait time）很常且變化很大
- IO 的使用效率上都蠻差的

雖然以上缺點，不過因為**可預測**所以還是有用。

## 輪流（Round Robin；RR）
和 FCFS 基本相同，差別是會設定一個固定的 time quantum，如果目前行程的 time quantum 就放到佇列的後面重新排隊。

如果 time quantum 設定超大，就等同是 FCFS。

如果 time quantum 設定超小，就會讓 context switch 太頻繁。

## SJF = Shortest Job First
簡單（花的時間少）的 process 先做。

證明 SJF 是 optimal（最短的 average waiting time）。每次交換相鄰且順序是「長短」的兩個 process 成「短長」，如此就會讓 average waiting time 變小。

優點：
1. 最短的平均等待時間

缺點：
有些要花比較多時間的可能就要一直等待，甚至永遠無法執行（starvation）

### SRJF = Shortest Remaining Job First
考慮到目前執行的 process 還**剩下**多少時間，如果有新的 process 比她還要少就 preempt。


## Multi Queue
多條 ready queue，並採用不同的演算法。

根據行程的類型和重要程度放到不同的 ready queue。

作業系統會隨機（每條 ready queue 的機率由設計者決定）挑選 queue 中的行程執行。

## Multi Feedback Queue
和 multi queue 基本相同，差別是在不是隨機挑選，而是從上而下（如果上面的 queue 不為空就必須要選，直到空才往下執行）。

大部分系統採用此方式。



# Multiple Processor 排程（硬體排程）
![](./Screenshot%20from%202023-10-11%2011-58-58.png)

## SMP
兩個 CPU 各自有 cache，透過 bus 共用一個 memory。


## NUMA = non uniform memory access

對於靠近自己的記憶體存取速度比較快（可以看鳥哥那篇）。

![](./Screenshot%20from%202023-10-11%2012-01-23.png)

topology 有幾種選擇：ring、mesh、**hypercube**

補充：hypercube 是什麼？


# Load Balancing

process affinity：如果都綁在同一個 core 上面，cache 可以重複使用，提高執行效率。相反的 process migration 需要時間，所以通常希望不要把這個 processor 的 process 移動到其他的 processor。

load balancing：因為通常會傾向將

所以要再 process affinity 和 load balancing 之間作取捨。以 Linux 為例，每 200ms 做一次 push migration（把 heavy loading 的 processor 的 process 分到其他 processor），以及當有 processor 的 ready queue 是空的會把其他（heavy loading）的 processor 的 process 給她。


# Multicore

multicore processor：有多個實體的運算單元（core，可以想成就是一個 CPU），所有運算單元共用同一塊 Last Level Cache (LLC)。


multithreading processor：多的 logical processor（實體只有一個）有不同的 registers。
> 這邊的 thread 和前面說的不是同一個東西！

> hyperthreaded

![](./Screenshot%20from%202023-10-23%2015-28-12.png)
![](./Screenshot%20from%202023-10-23%2015-33-35.png)

在平衡上面，首先平衡 physical 才是平衡 logical。


## 以我的電腦為例

總 logical CPU 數量 = 總 physical CPU 數量 X 每個 physical CPU 的核数（core） X 超線程數（hyper thread）

top 看到的 CPU（8 個）指的是 logical CPU 的個數。

`cat /proc/cpuinfo`

會有八段資訊（八個 logical）。

一顆 physical CPU，四核心。（所以是 2 threads？）

```
processor	: 0
vendor_id	: GenuineIntel
cpu family	: 6
model		: 142
model name	: Intel(R) Core(TM) i5-8265U CPU @ 1.60GHz
stepping	: 11
microcode	: 0xf4
cpu MHz		: 1800.000
cache size	: 6144 KB
physical id	: 0
siblings	: 8
core id		: 0
cpu cores	: 4
apicid		: 0
initial apicid	: 0
fpu		: yes
fpu_exception	: yes
cpuid level	: 22
wp		: yes
flags		: fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm constant_tsc art arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf pni pclmulqdq dtes64 monitor ds_cpl vmx est tm2 ssse3 sdbg fma cx16 xtpr pdcm pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm 3dnowprefetch cpuid_fault epb invpcid_single ssbd ibrs ibpb stibp tpr_shadow vnmi flexpriority ept vpid ept_ad fsgsbase tsc_adjust bmi1 avx2 smep bmi2 erms invpcid mpx rdseed adx smap clflushopt intel_pt xsaveopt xsavec xgetbv1 xsaves dtherm ida arat pln pts hwp hwp_notify hwp_act_window hwp_epp md_clear flush_l1d arch_capabilities
vmx flags	: vnmi preemption_timer invvpid ept_x_only ept_ad ept_1gb flexpriority tsc_offset vtpr mtf vapic ept vpid unrestricted_guest ple pml ept_mode_based_exec
bugs		: spectre_v1 spectre_v2 spec_store_bypass mds swapgs itlb_multihit srbds mmio_stale_data retbleed gds
bogomips	: 3600.00
clflush size	: 64
cache_alignment	: 64
address sizes	: 39 bits physical, 48 bits virtual
power management:
```


# Linux 的排程演算法 - CFS

completely fair scheduler

virtual runtime（virtual clock 演算法）

給予每個 process 不同的 rate。

![](./Screenshot%20from%202023-10-23%2016-04-09.png)

nice value = increasing rate of vruntime

![](./Screenshot%20from%202023-10-23%2016-04-09.png)

每次如何挑選最小的 vruntime？紅黑樹！

# 問題
FCFS 有什麼 performance issue？
1. Poor I/O utilization -> 對
2. Low CPU utilization -> 錯（基本上只要有 process，CPU 都會在使用）
3. Poor interactivity -> 對（大部分都是 I/O-bound process）





# 參考

- http://old.linux.vbird.org/linux_enterprise/cputune.php