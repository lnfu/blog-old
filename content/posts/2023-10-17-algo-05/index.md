---
title: "[課程筆記] 演算法概論（五） - Hash Table"
date: 2023-10-17T10:39:09+08:00
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

IUH = independently uniform hashing

# hash function

一種作法：
- w 是系統的位元
- A 是 (0, 1) 之間的某個數（Knuth 建議選擇黃金分割數）
- k 是 key

![](./Screenshot%20from%202023-10-17%2011-22-40.png)

# universal hashing

定義？

https://www.zhihu.com/question/50108837

# 處理 collision 問題

## Chaining

補機率分析

