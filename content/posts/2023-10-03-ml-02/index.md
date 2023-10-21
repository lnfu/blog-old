---
title: "[課程筆記] 機器學習概論（二） - 資料種類"
date: 2023-10-03T14:53:16+08:00
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

[[課程筆記] 機器學習概論（一） - 機器學習簡介](../2023-10-03-ml-01/)

[[課程筆記] 機器學習概論（二） - 資料種類](../2023-10-03-ml-02/)

[[課程筆記] 機器學習概論（三） - 資料探索](../2023-10-03-ml-03/)

[[課程筆記] 機器學習概論（四） - Decision Tree](../2023-10-17-ml-04/)

---

這次講資料的種類和形式。

ABT：analytics base table
![](./Screenshot%20from%202023-10-03%2015-00-34.png)

# 資料種類

## Nominal（Categorical）
分類。例如人種、性別。（沒有數學解釋）

### Binary
只有兩種值的 nominal variable。例如答案是正確/錯誤、性別是男/女。

## Ordinal
和 nominal variable 有點像，差別是有順序（order）。例如衣服尺寸有 small/medium/large。

除了順序以外沒有其他任何數學解釋，所以我們不能隨便用一個數字就去代表，否則會有問題。

例如假設我們有兩群資料，第一群的尺寸 small/medium/large 分別用 1/2/3 代表。
第二群分別用 1/20/20.5 代表。

這樣都滿足順序，然而不代表第二群的平均比第一群的高，只是因為表示上造成的錯誤。


## Integer
整數

## Ratio-Scaled
比率

## Interval-Scaled
區間。例如溫度是和 0 度之間的差。

## Textual
文字



# 資料形式

## Matrix
像是 table（e.g., ABT）

## String
A sequence of symbols from some **finite** alphabet.

例如英文句子、生物序列。


## Event Sequence
一堆 {event, time} 組成的序列。例如交易。



# 時間資料的問題

![](./Screenshot%20from%202023-10-03%2015-26-30.png)

假設是臨床試藥，每一筆資料代表一個試驗者，中間的星星就是服用藥物的瞬間。（上面的圖是理想，下面是實際）。

![](./Screenshot%20from%202023-10-03%2015-29-33.png)

除此之外，有時候我們只要 observation period 沒有 outcome period，或是有時候我們只要 outcome period。


