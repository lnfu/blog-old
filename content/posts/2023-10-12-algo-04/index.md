---
title: "[課程筆記] 演算法概論（四） - Median Finding"
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

[[課程筆記] 演算法概論（一） - Introduction](../2023-08-25-algo-01/)

[[課程筆記] 演算法概論（二） - Divide & Conquer](../2023-09-12-algo-02/)

[[課程筆記] 演算法概論（三） - Sorting](../2023-09-19-algo-03/)

[[課程筆記] 演算法概論（四） - Median Finding](../2023-10-12-algo-04/)

[[課程筆記] 演算法概論（五） - Hash Table](../2023-10-17-algo-05/)

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

接下來進入到我們的主題：給定一個序列，找出第 k 小的數字。

# RANDOMIZED-SELECT 演算法

找出 A 序列中索引 p 和 r 之間第 i 小的數。

和 QUICKSORT 演算法基本是一樣，差別只在 QUICKSORT 會遞迴左右兩邊的子序列，而 RANDOMIZED-SELECT 只會選擇要找的數字的那一邊去遞迴。

```
p       q       r
|  ...  |  ...  |
```

```
RANDOMIZED-SELECT(A, p, r, i)
1   if p = r
2   return A[p]
3   q = RANDOMIZED-PARTITION(A, p, r)
4   k = q - p + 1
5   if i = k
6       return A[q]
7   elif i < k
8       return RANDOMIZED-SELECT(A, p, q - 1, i)
9   else
10      return RANDOMIZED-SELECT(A, q + 1, r, i - k)
```

> 1 index

## RANDOMIZED-SELECT 時間複雜度分析

最差情況下，我們每次分區都分成 1 和 n - 1 兩堆，每次所有數字都要和 pivot 比較。
```
T(n) = T(n - 1) + O(n)
T(n) = T(n^2)
```

平均情況，之後再研究（有點複雜，要用到期望值的概念，請看投影片 p.8）。


# 分堆作法 SELECT(A, k)

假設 A 陣列總共有 n 個數，要找到第 k 小個數：
1. 先分成 n / r 堆（r 是奇數）
2. 每堆（r 個數字）先排序好，並且標出中位數（e.g., r = 5 每堆的第 3 個是中位數）
3. 對於這些中位數（用 M 表示由他們組成的陣列）找出他們的中位數（用 SELECT(M, M/2) 下去遞迴），假設這個數字是 m
4. 這樣就可以把所有數字分成小於、等於、大於 m 的三群
5. 根據這樣的資料我們可以找到（介在等於那個區間）或是繼續遞迴（介在小於或是大於的區間）得到第 k 小的數字。

## 分堆算法的時間複雜度分析

每個子陣列有 r 個元素，總共有 n / r 個陣列，所以第 2 個步驟是 `O(r) * (n / r) = O(n)`（r 是常數）。

總共有 n / r 個中位數，所以第 3 個步驟是 `T(n / r)`。

第 4 個步驟是 `O(n)`。（所有數字掃過一遍）。

第 5 個步驟是 `T(3n/4)`（每次可以踢掉超過 1/4 的數字，可以看圖知道）。

```
T(n) = O(n) + T(n / r) + O(n) + T(3n/4)
T(n) = O(n)
```

> 通分 `T(n/r) + T(3n/4)` 在去求等比級數的極限就會得到 `O(n)`