---
title: "最大連續子區間和"
date: 2023-09-30T21:03:38+08:00
draft: false
author: "Enfu Liao"
tags: ["演算法"]

# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

每次看完就忘記，還是寫下來好了。

題目是這樣：**給定一個序列，求連續且不為空的最大區間和，以及其頭尾的索引值。**

有兩種解法，一種用 Prefix Sum，另外一種用 Dynamic Programming（DP）。

要注意求和的那些變數可能會 overflow 所以要開大一點。

# 參考
* [春水煎茶 · 王超的个人博客 - 最大连续子序列和问题](https://writings.sh/post/algorithm-largest-sum-contiguous-subarray)
* [GeeksforGeeks - Largest Sum Contiguous Subarray (Kadane’s Algorithm)](https://www.geeksforgeeks.org/largest-sum-contiguous-subarray/)