---
title: "[課程筆記] 演算法概論（七） - 最佳二分搜尋樹（OBST）"
date: 2023-10-31T10:29:48+08:00
draft: false
author: "Enfu Liao"
tags: ["課程筆記", "演算法"]

# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

BST = Binary Search Tree

前幾次上課有講 hash，不過因為內容都蠻 trivial 所以我就不紀錄了。

這邊還是稍微帶過一下 hash collision 的處理方式。

- chaining
- open addressing
    - linear probing
    - double hashing

如果是使用 open addressing，在執行刪除操作（delete）時會有問題。

假設是 `h = k % 9`，並且插入 5、14。

因為他們 hash 都是 5 所以 14 會放在別的位置。

今天我如果刪除 5，之後要刪除或是查詢 14 時會因為 5 是空的所以認為沒有這個數字，從而產生錯誤。

因此，對於需要頻繁插入、刪除的情況，我們需要引入新的東西：BST。

不過這章不會講什麼是 BST（說這麼多結果不講哈哈），而是 OBST（optimal binary search tree）。

# Complete vs Full

Complete: leaf 都要在最底部
Full: 所有 internal node 都要有 2 個 children