---
title: "[課程筆記] 機器學習概論（四） - Decision Tree"
date: 2023-10-17T13:28:00+08:00
draft: false
author: "Enfu Liao"
math: true

# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

[[課程筆記] 機器學習概論（一）](../2023-10-03-ml-01/)

[[課程筆記] 機器學習概論（二）](../2023-10-03-ml-02/)

[[課程筆記] 機器學習概論（三）](../2023-10-03-ml-03/)

[[課程筆記] 機器學習概論（四）](../2023-10-17-ml-04/)

---

DT（desion tree；決策樹）或是叫做 classification tree

假設我們的資料如下：
![](./Screenshot%20from%202023-10-17%2014-05-52.png)

從 root 開始不斷根據 descriptive features 去分割（分類）就可以得到像是底下這樣的 DT。

![](./Screenshot%20from%202023-10-17%2014-07-21.png)

因為分割的方式（DT）可以用很多種（像是上圖就展示了 (a) 和 (b) 兩種），我們需要有一個數值來量化，什麼樣的分割是比較好的，一種量化方式是使用 IG（Information Gain；資訊增益、資訊獲利）。

# IG 
IG 越大越好。

{{< math_block >}}
IG(D_{p}, f) = I(D_{p}) - \sum_{j=1}^{m} \frac{N_{j}}{N_{p}} I(D_{j}) 
{{< /math_block >}}

- {{< math_inline >}}IG(D_{p}, f){{< /math_inline >}}：資訊增益
- {{< math_inline >}}I(D_{p}){{< /math_inline >}}：原本的資訊量
- {{< math_inline >}}\sum_{j=1}^{m} \frac{N_{j}}{N_{p}} I(D_{j}){{< /math_inline >}}：切割後所有資訊量的加權總和

在計算資訊量上，有兩種面向：
1. Entropy（熵）
2. Gini Impurity

## Entropy

{{< math_block >}}I_{H} = - \sum_{i=1}^{c} p(i|t) \log_{2} p(i|t){{< /math_block >}}

Why 如此定義？

https://hackmd.io/@8dSak6oVTweMeAe9fXWCPA/ryoegPgw_

## Gini Impurity

老師沒教。

{{< math_block >}}I_{G} = \sum_{i=1}^{c} p(i|t) (1 - p(i|t)) = 1 - \sum_{i=1}^{c} (p(i|t))^{2}{{< /math_block >}}

> 關於上式的化簡，別忘記全部機率相加會是 1

# 演算法

有了 IG 能夠量化什麼樣的分割（分類）會是最好的，就可以直接用 Greedy 的方式（遞迴）找出最佳的 DT。

```
1   選擇目前資料集所有分類最好的分類將資料集拆成多個子資料集
2   對每個子資料集重複 1
3   當資料集的所有資料都屬於某一種分類，停止遞迴
```



