---
title: "[課程筆記] 演算法概論（四）"
date: 2023-10-12T15:58:08+08:00
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

前面的 FFT 和 sorting 漏掉很多，之後再補＆整理！

這次講解找中位數的問題（和演算法）。

最簡單的想法就是利用前一章的各種 sorting 演算法排序完然後找中間那個數字就是答案，不過是否有更快的解法？



# 先從找最大值與最小值開始

比起中位數，找出未排序的數列中最大值和最小值顯然比較容易一點。

假設我們要找到某個數列的最小值，可以用一個變數 `min` 去紀錄當前的最小值（預設值設成無限大），然後遍歷這個數列每次比較當前數字和 `min`，如果比 `min` 小就把 `min` 設成它。

這樣的時間複雜度會是 `n`（走過一次整個數列）。

如果我們想要同時找到最大和最小值呢？最簡單的想法是直接紀錄兩個數字 `min` 和 `max` 然後採用剛剛講的作法去跑一次。

這樣的時間複雜度會是 `2n`（每次都要比較最大和最小值）。

我們也可以稍微優化一下達到 `3*n/2`，作法是這樣子：把所有數字兩兩一組，每一組先各自和對方比較，比較小的再和 `min` 比較，比較大的和 `max` 比較。

---









在開始之前先引入一個隨機演算法：RANDOMIZED-SELECT

# RANDOMIZED-SELECT 演算法

**回去弄懂！**

```
p       q       r
|  ...  |  ...  |
```

```
RANDOMIZED-SELECT(A, p, r, i)
1.  if p == r
2.      return A[p]
3.  q = RANDOMIZED-PARTITION(A, p, r)
4.  k = q - p + 1
    if i == k
        return A[q]
    elseif i < k
        return RANDOMIZED-SELECT(A, p, q - 1, i);
    else
        return RANDOMIZED-SELECT(A, q + 1, r, i - k);
```



**分析還沒寫！**


# 分堆作法

先分成五堆，找出每堆的中位數。


