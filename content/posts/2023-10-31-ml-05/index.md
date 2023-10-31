---
title: "[課程筆記] 機器學習概論（五） - 集成學習（Ensemble Learning）"
date: 2023-10-31T13:25:14+08:00
draft: false
author: "Enfu Liao"
tags: ["課程筆記", "機器學習"]

# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

一次使用多個分類器。

就像是一次詢問多個專家去解決問題。

# Bagging

Bootstrap 抽樣：抽完後會放回去

假設有 m 個分類器，資料集總共有 N 筆資料。

我們從資料集抽出 n 個給第一個分類器再放回去，然後再抽 n 個給第二個分類器放回去，一直作到第 m 個分類器。


## Random Forest

Bagging + Decision Tree

```
Dataset --> Training Set 1 --> Tree 1
        \
        |-> Training Set 2 --> Tree 2
        \
        |-> Training Set 3 --> Tree 3
        .
        .
        .
        \
         -> Training Set m --> Tree m
```