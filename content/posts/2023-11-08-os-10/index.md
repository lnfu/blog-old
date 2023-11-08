---
title: "[課程筆記] 作業系統概論（十） - 記憶體"
date: 2023-11-08T10:22:06+08:00
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

# Address Binding

可以在三個階段做 Address Binding：
1. compile time: 在編譯時期就決定好 address（absolute addressing），缺點是如果有兩個程式位置相同就會有 conflict，所以不能同時載入這兩個程式。
2. load time: 和 PC 的相對位置，PC-relative addressings。（實際在算的時候 PC 已經 + 2，所以應該是 PC + 2 - n）
3. execution time: 需要 MMU 硬體支持（才夠快）

![execution time address binding](./Screenshot%20from%202023-11-08%2010-47-10.png)

## Logical vs Physical

logical address: CPU 以為的位置
physical address: 實際在記憶體的位置

## MMU
memory management unit

一定是硬體，軟體做不了（太慢）

功能有：
1. relocation（剛剛提的）
2. paging
3. segmentation

![](./Screenshot%20from%202023-11-08%2010-49-56.png)



# 記憶體分配

process 放到記憶體時，一定要找到夠大的連續記憶體放大（不能把 process 拆成兩半之類的），不過當 process 執行完後就會留下一個空隙。

難點：
1. 所有 process 需要的記憶體大小不同
1. proess 要求的記憶體必須連續

這樣會造成可能明明記憶體空的所有區間加起來是足夠的，但是這些空隙單一個都太小放不下 process。

![](./continuous%20allocation.png)

Dynamic 記憶體分配是 NP-Complete 問題。

選擇要放哪個空格有三種 policy（藍色是 process，白色是空隙）：
![](./Screenshot%20from%202023-11-08%2010-59-14.png)

以上圖來說：
1. Best fit 會剩下 1 KB
2. Worst fit 會剩下 7 KB
3. First fit 會剩下 2 KB

如果只看速度 first fit 會比較快。

「一般來說」，best fit 效果會比較好。

因為永遠可以找到情況是 A 好 B 壞，記得練習找例子（誰好誰壞）。

另外除了上面很直覺的三種，還有另一種: Buddy System。

## Buddy System
都用二分去切，放的時候直接由左到右放在對應 size 的空格。

process 結束後要 free 並且如果 buddy（旁邊的老哥）也是 free 就可以合併。

![](./Screenshot%20from%202023-11-08%2011-29-31.png)

實際的在儲存的資料結構就用一個 linked list，每個元素指向每種 size 的 linked list。

![](./buddy%20system.png)


特色：
1. 快速，超級快（linked list 去找）
2. 如果要分配的 size 不是 2 冪次，就只能分配大一點，所以會浪費（e.g., 65 -> 128）
3. 會有很多切塊後的空隙（明明應該可以是一整塊）

結論：
浪費蠻多記憶體，但是因為快速所以 Linux 是使用這個（記憶體便宜麻，快比較重要）。


