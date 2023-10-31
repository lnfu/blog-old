---
title: "[課程筆記] 機器學習概論（六） - NN"
date: 2023-10-31T15:31:22+08:00
draft: true
author: "Enfu Liao"
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---


相似度（Similarity）

對每個資料找出在資料集中相似的那些資料，形成一個區域（neighborhood）。

一種作法：對於一個未知的 x 來說，她所在的分類就是她鄰居中最多的那一類。例如 x 的鄰居大部分都是男生，那麼 x 就是男生。

# 如何決定誰是你的鄰居（neighbor）？

距離（distance）

或是 metric

Metric和distance 基本上一樣，不過定義上要滿足四個條件：
1. ..
2.
3.
4.


計算距離（or metric）的方式有很多（演算法）。

## Minkowski Distance

...

歐幾里德距離是 p = 2 的特例
曼哈頓距離是 p = 1 的特例


## Cosine Similarity


...


兩個點的對x軸夾角的差

![](./Screenshot%20from%202023-10-31%2015-58-52.png)


## 對於 symbolic 的資料？

對於像是衣服尺寸，要如何計算？

...



# Nearest Neighbor Algo（NN）

什麼情況下適合這種演算法？

當
- feature 數量比較少時（<= 20）
- 訓練資料集相對較大時


優點：
1. 訓練快速
2. no info loss


缺點：
1. slow in prediction
2. 對於不相關的 feature 很容易考慮進去：metric 是我們設計者決定，假設我們會把全部的 feature 都納入 metric 的計算裡，就有可能用到沒有關聯的 feature。
3. 沒有建立 model（這樣為什麼是缺點？）



## 加權 kNN

讓比較近的鄰居的影響更大


## 連續 target value

可以直接去計算所有鄰居的數值的平均

或是也可以加權


# feature 選擇

## search

### forward search

先找出最好的 feature 去做，之後在找出第二好的



### backward search


先找出最爛的（無關的 feature）挑掉，之後再找出第二爛的


### bidirectional search

forward 和 backward 同時做（找出最相關的同時也剔除不相關的）




## method


![](https://www.researchgate.net/publication/308574147/figure/fig3/AS:412135269126149@1475272113423/Three-types-of-feature-selection-a-Filter-b-Wrapper-c-Embedded.png)


### filter

independent of learning algo 

更有效率

### wrapper

慢

更好的 inductive bias

<!-- ### embedded
好像沒說
feature selection 和訓練同時進行。 -->

