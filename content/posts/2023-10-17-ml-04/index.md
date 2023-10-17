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

分割的方式（DT）可以用很多種（像是上圖就展示了 (a) 和 (b) 兩種），我們需要有一個方式來量化什麼樣的分割是比較好的。

所以根據不同的量化方式，我們會有不同生成 DT 的演算法。

這邊會介紹三種演算法以及其使用來量化分割的 metric（度量指標）：
- ID3：使用 IG（Information Gain；資訊增益、資訊獲利）
- CART：使用 Gini Index
- C4.5：使用 IGR（Information Gain Ratio）

# 演算法

有了 metric 能夠量化什麼樣的分割（分類）會是最好的，就可以直接用 Greedy 的方式（遞迴）找出最佳的 DT。
```
Main loop:
1   A <- the "best" decision attribute for next node
2   Assign A as decision attribute for node
3   For each value of A, create new descendant of node
4   Sort training examples to leaf nodes
5   If training examples perfectly classified, Then STOP, Else iterate over new leaf nodes

```

白話一點說就是以下方法：
1. 選擇目前資料集所有分類最好的分類將資料集拆成多個子資料集
2. 對每個子資料集重複 1
3. 當滿足以下條件時停止遞迴
    1. 資料集的所有資料的 target value（target feature）都相同
    2. 已經沒有 descriptive features 可以用來分類了
    3. 資料集是空的

補充：在同一個 branch 上面使用同一個 descriptive features 多次是沒有意義的（可以做只是沒有任何幫助）。可以參考：https://stackoverflow.com/questions/19993139/can-splitting-attribute-appear-many-times-in-decision-tree


# ID3
IG 越大越好。

{{< math_block >}}
IG(D_{p}, f) = I(D_{p}) - \sum_{j=1}^{m} \frac{N_{j}}{N_{p}} I(D_{j}) 
{{< /math_block >}}

- {{< math_inline >}}IG(D_{p}, f){{< /math_inline >}}：資訊增益
- {{< math_inline >}}I(D_{p}){{< /math_inline >}}：原本的資訊量
- {{< math_inline >}}\sum_{j=1}^{m} \frac{N_{j}}{N_{p}} I(D_{j}){{< /math_inline >}}：切割後所有資訊量的加權總和

透過計算 Entropy（熵）表示資訊量上。

## Entropy

{{< math_block >}}I_{H} = - \sum_{i=1}^{c} p(i|t) \log_{2} p(i|t){{< /math_block >}}

Why 如此定義？

https://hackmd.io/@8dSak6oVTweMeAe9fXWCPA/ryoegPgw_

## 缺點

IG 會傾向使用有很多 value 的 descriptive features，例如使用者 ID，然而我們都知道用使用者 ID 去分類是毫無意義的。

# CART


# C4.5

{{< math_block >}}
GR(D, A) = \frac{IG(D, A)}{SplitInfo(D, A)}
SplitInfo(D, A) = - \sigma_{i=1}^{c} \frac{|D_{i}|}{|D|} log_{2}{\frac{|D_{i}|}{|D|}}
{{< /math_block >}}

## 缺點

不太清楚...

Prefers features that produce uneven splits, e.g.,{{< math_inline >}}\frac{|S_{1}|}{|S|} >> \frac{|S_{2}|}{|S|}{{< /math_inline >}}



# 參考
- [資料分析系列-探討決策樹(1)](https://medium.com/%E4%BC%81%E9%B5%9D%E4%B9%9F%E6%87%82%E7%A8%8B%E5%BC%8F%E8%A8%AD%E8%A8%88/%E8%B3%87%E6%96%99%E5%88%86%E6%9E%90%E7%B3%BB%E5%88%97-%E6%8E%A2%E8%A8%8E%E6%B1%BA%E7%AD%96%E6%A8%B9-1-1cc354484559)


---

{{< math_block >}}I_{G} = \sum_{i=1}^{c} p(i|t) (1 - p(i|t)) = 1 - \sum_{i=1}^{c} (p(i|t))^{2}{{< /math_block >}}

> 關於上式的化簡，別忘記全部機率相加會是 1
