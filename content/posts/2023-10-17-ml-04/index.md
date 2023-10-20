---
title: "[課程筆記] 機器學習概論（四） - Decision Tree"
date: 2023-10-17T13:28:00+08:00
draft: false
author: "Enfu Liao"
math: true
tags: ["課程筆記", "機器學習"]
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
- ID3：使用 IG（Information Gain；資訊增益、資訊獲利）with **Entropy**
- CART：使用 IG with **Gini Index**
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

> 注意，以下的 {{< math_inline >}}c{{< /math_inline >}} 都代表 classification 的數量

# ID3
IG 越大越好。

{{< math_block >}}
IG(D_{p}, A) = Entropy(D_{p}) - \sum_{j=1}^{m} \frac{N_{j}}{N_{p}} Entropy(D_{j}) 
{{< /math_block >}}

- {{< math_inline >}}IG(D_{p}, A){{< /math_inline >}}：資訊增益
- {{< math_inline >}}Entropy(D_{p}){{< /math_inline >}}：原本的資訊量（透過計算 Entropy（熵）表示）
- {{< math_inline >}}\sum_{j=1}^{m} \frac{N_{j}}{N_{p}} Entropy(D_{j}){{< /math_inline >}}：切割後所有資訊量的加權總和

## Entropy

{{< math_block >}}
Entropy(D) = - \sum_{i=1}^{c} p(i|t) \log_{2} p(i|t)
{{< /math_block >}}

Why 如此定義？

https://hackmd.io/@8dSak6oVTweMeAe9fXWCPA/ryoegPgw_

## 缺點

這樣算出的 IG 會傾向使用有很多 value 的 descriptive features，例如使用者 ID，然而我們都知道用使用者 ID 去分類是毫無意義的。

# CART

{{< math_block >}}
IG(D_{p}, A) = Gini(D_{p}) - \sum_{j=1}^{m} \frac{N_{j}}{N_{p}} Gini(D_{j}) 
{{< /math_block >}}

{{< math_block >}}
Gini(D) = \sum_{i=1}^{c} p(i|t) (1 - p(i|t)) = 1 - \sum_{i=1}^{c} (p(i|t))^{2}
{{< /math_block >}}

> 關於上式的化簡，別忘記全部機率相加會是 1

# C4.5

{{< math_block >}}
GR(D, A) = \frac{IG(D, A)}{SplitInfo(D, A)}
SplitInfo(D, A) = - \sigma_{i=1}^{c} \frac{|D_{i}|}{|D|} log_{2}{\frac{|D_{i}|}{|D|}}
{{< /math_block >}}

## 缺點

不太清楚...

Prefers features that produce uneven splits, e.g.,{{< math_inline >}}\frac{|S_{1}|}{|S|} >> \frac{|S_{2}|}{|S|}{{< /math_inline >}}


# Continuous Descriptive Features

上面討論的都是建立在我們的 descriptive feature 是離散的資料（e.g., 性別 - 男/女）如果是連續的資料，我們可以把它拆成不同的區間再用同樣的方法生成 DT（離散化）。  

不過要怎麼決定該如何拆？例如應該把體重拆成大於 50 和小於等於 50，還是大於 60 和小於等於 60？

```
1   Sort the instances according to the continuous values.
2   Identify the adjacent values that have different classifications.
3   Find the best threshold by computing the IG for each of these class transition boundaries and selecting the boundary with the highest IG as the threshold
4   Let the feature (from 3) compete with other available features as the splitting feature for the node.
5   Repeat the same procedure at each node as the tree grows.
```

白話文就是先把連續型的資料 sort 過後，然後每次去選擇一個 threshold 把資料切成兩塊再去計算其 IG，然後用 IG 最小的 threshold 作為一個新的 descriptive feature 再去和其他的 descriptive features 比較。

# Continuous Target Features - Regression Tree

之後補

![](./Screenshot%20from%202023-10-17%2015-18-22.png)


# Overfitting

指模型對於範例的過度訓練，導致模型記住的不是訓練資料的一般特性，反而是訓練資料的局部特性。

![](./Screenshot%20from%202023-10-17%2015-33-18.png)

原因：
1. 雜訊
2. 資料太少

解決方式：Pruning（剪枝）。

> pruning 是解決雜訊問題？

分成：
- prepruning
- postpruning

## Prepruning

每次往下一層（生成樹）時，如果預先定義的臨界值（最簡單就是直接看 IG）低於某個數值就停止往下。  

### chi-square test（卡方檢定）

{{< math_block >}}
\chi^{2} = \sum_{i} \frac{(O_{i} - E_{i})^{2}}{E_{i}}
{{< /math_block >}}

- {{< math_inline >}}\Chi^{2}{{< /math_inline >}}：chi squared
- {{< math_inline >}}O_{i}{{< /math_inline >}}：觀測值
- {{< math_inline >}}E_{i}{{< /math_inline >}}：期望值

舉例來說，假設 A1 節點會分成四個 v1, v2, v3, v4 節點，每個節點內的資料分成 C1, C2 兩種（target）。

| A1            | v1  | v2  | v3 | v4  | Total |
|---------------|-----|-----|----|-----|-------|
| C1 (positive) | 68  | 75  | 57 | 79  | 279   |
| C2 (negative) | 32  | 45  | 33 | 31  | 141   |
| Total         | 100 | 120 | 90 | 110 | 420   |

A1 中 C1 和 C2 的比例是 {{< math_inline >}}\frac{279}{420}:\frac{141}{420}{{< /math_inline >}}

所以 v1 的 C1 期望值是 {{< math_inline >}}100 \cdot \frac{279}{420}{{< /math_inline >}}，C2 期望值是 {{< math_inline >}}100 \cdot \frac{141}{420}{{< /math_inline >}}...

假設卡方檢定告訴我們 A1 的分法和結果（C1, C2）independent，我們就不需要 A1。

## Postpruning

先生成好 DT 再去試著剪她（從下往上剪），如果 validation 結果是好的就確定剪下去。

怎麼做 validation？traning 和 validation 都有一個 dataset，並且這兩個互相 exclusive。

每次去比較剪枝前和剪枝後用 validation dataset 跑過得 accuracy，如果 accuracy 變好就能確定這個剪枝合適。

# 參考
- [資料分析系列-探討決策樹(1)](https://medium.com/%E4%BC%81%E9%B5%9D%E4%B9%9F%E6%87%82%E7%A8%8B%E5%BC%8F%E8%A8%AD%E8%A8%88/%E8%B3%87%E6%96%99%E5%88%86%E6%9E%90%E7%B3%BB%E5%88%97-%E6%8E%A2%E8%A8%8E%E6%B1%BA%E7%AD%96%E6%A8%B9-1-1cc354484559)
- [聯合大學 slide](http://debussy.im.nuu.edu.tw/sjchen/MachineLearning/final/CLS_DT.pdf)





---

