---
title: "[課程筆記] 機器學習概論（三） - 資料探索"
date: 2023-10-03T15:39:50+08:00
draft: false
author: "Enfu Liao"
tags: ["課程筆記", "機器學習"]
math: true
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

這次講的和資料的分析有關。

> 探索式資料分析（EDA，Exploratory Data Analysis）透過*傳統統計方法*與*視覺化方法*幫助資料分析者觀察理解資料。在進行複雜或嚴謹的分析模型之前，能夠對資料有初步的認識，有助於判斷適合的資料預處理與特徵工程策略。參考自：https://www.instagram.com/p/CgPTcn7vELD/


data summary = data quality report

包含每個 feature 的統計測量值，像是平均數（Mean）、中位數（Median）、眾數（Mode）、標準差（Standard Deviation）、極值（最大/最小值），以及 data visualization（例如 histogram）。

![](./Screenshot%20from%202023-10-03%2015-42-59.png)

![](./Screenshot%20from%202023-10-03%2015-47-02.png)


# Data Quality Issues
anything unusual about the data in an ABT

常見的有：
1. missing values
2. irregular cardinality：不同值的數量異常。例如，對於連續型的資料，不同值的數量應該會趨近資料的筆數，如果太少就算是異常。又例如一個誇張的異常是某個 feature 的值全部相同。又例如關於生理性別，理論上只會有兩種值（男、女），如果有三種不同的值就是 irregular cardinality。
3. 異常值（outliers）：例如某個值異常的大


## Missing Values

### 種類

#### MCAR = missing completely at random
此種缺失機制是假設缺失資料是隨機出現，與觀察到及未觀察到的資料皆無關。

受試者缺失資料的產生與本身健康狀態與試驗藥物沒有任何關係。例如受試者因為搬家的原因無法繼續參與試驗，或問卷未翻譯成該國語言導致受試者看不懂而無法回答。

#### MAR = missing at random
缺失的發生和缺失的資料無關。或者說是缺失資料的發生與觀察到的資料有關，但是與未觀察到的資料無關。

消失的原因可以由其他 feature 推出來。

例如糖尿病臨床試驗，受試者若試驗期間空腹血糖值超過 270 mg/dl， 則須要退出試驗。

#### MNAR = missing not at random
缺失資料的發生與缺失資料本身的觀察值有關。

消失的原因無法由其他 feature 推出來。

例如高所得者普遍傾向於拒絕回答收入的問題。


### 解決方法
1. （如果太多）刪除直排（feature）
2. （如果太多）刪除橫排（record）
3. 隨便填（例如所有 missing 都填入 0）
4. imputation：很多種。

#### imputation
single vs. multiple
- single：例如填入平均數、中位數
- multiple：例如 MCMC

> 有時間去搞懂 MCMC

global vs. local
- global：例如 PCA（principle component analysis）
- local：例如從 neighbor 的值來 impute

> 有時間去搞懂 PCA

discriminative vs. generative
- discriminative：例如 MissForest
- generative：例如 GAIN

> 有時間去搞懂 MissForest、GAIN


![](./Screenshot%20from%202023-10-03%2019-22-21.png)



## Outliers
可以設定超過 upperbound 就變成 upperbound，少於 lowerbound 就變成 lowerbound。

upperbound 和 lowerbound 的選擇：
- 根據四分位數
- 68-95-99.7

# 進階 EDA：視覺化方法

視覺化（兩個或甚至多個）feature 之間的關係。

## 連續 + 連續
對於兩個都是連續的 feature，可以使用 scatter plot（一張圖就夠了）。如果有很多個連續的 feature，可以兩兩一組來分析（用一個 matrix of scatter plots 表示）。
![](./Screenshot%20from%202023-10-21%2002-00-44.png)


## 離散 + 離散
對於兩個都是分類（categorical）的 feature，可以用多張 bar plot 表示。
例如 Career Stage 有三種值（mid-career, rookie, veteran）、Shoe Sponsor 有兩種值（yes, no），就有兩種分析方式：
1. 3 = 1（全體）+ 2（Shoe Sponsor = yes/no），下圖
2. 4 = 1（全體）+ 3（Career Stage = mid-career/rookie/veteran）

![](./Screenshot%20from%202023-10-21%2002-10-16.png)

# 連續 + 離散
如果一個是連續型，一個是分類型，就把連續型切成一個個的 interval（離散化），並且當作多張 bar plot 的 x 軸（y 軸是 density）。

這樣會有 1 + n（分類型變數值的數量）張圖。

![](./Screenshot%20from%202023-10-21%2002-16-38.png)

另外還有一種分析方式是畫成 box plot，分類型的值有幾種就會有幾個（再多一個全體） box。

![](./Screenshot%20from%202023-10-21%2002-20-27.png)

# 進階 EDA：統計分析

因為是樣本而不是母體，所以都是 {{< math_inline >}}n - 1{{< /math_inline >}} 而不是 {{< math_inline >}}n{{< /math_inline >}}，真正原因我也不知道。

如果獨立，那麼就不相關（反過來大錯特錯！）。因為相關只是討論線性回歸（一個變大另一個也變大/小）。

以下四張圖的 {{< math_inline >}}corr(A, B) = 0.816{{< /math_inline >}}。

![](./Screenshot%20from%202023-10-21%2002-40-11.png)

相關不等於因果（社會心理學有說）。

## Covariance（共變異數）
{{< math_block >}}
cov(a, b) = \frac{1}{n - 1} \sum_{i=1}^{n} ((a_{i} - \overline{a}) \times (b_{i} - \overline{b}))
{{< /math_block >}}

{{< math_block >}}
cov(a, b) \in [- \infty, + \infty]
{{< /math_block >}}

{{< math_block >}}
cov(a, a) = var(a)
{{< /math_block >}}

## Correlation（相關性）
{{< math_block >}}
corr(a, b) = \frac{cov(a, b)}{sd(a) \times sd(b)}
{{< /math_block >}}

{{< math_block >}}
corr(a, b) \in [-1, +1]
{{< /math_block >}}


- sd：標準差（Standard Deviation）

{{< math_block >}}
var(x) = \frac{1}{n - 1} \sum_{i=1}^{n} (x_i - \overline{x})^2
{{< /math_block >}}

{{< math_block >}}
sd(x) = \sqrt{var(x)}
{{< /math_block >}}

# 資料預處理

## Normalization

range-normalization（限定到 {{< math_inline >}}[low, high]{{< /math_inline >}}）：
{{< math_block >}}
a_{i}^{\prime} = \frac{a_{i} - min(a)}{max(a) - min(a)} \times (high - low) + low
{{< /math_block >}}

standardization：
{{< math_block >}}
a_{i}^{\prime} = \frac{a_{i} - \overline{a}}{sd(a)}
{{< /math_block >}}


## Binning
discretization

- equal-width
- equal-frequency

## Sampling

- top sampling：找前 {{< math_inline >}}s%{{< /math_inline >}} 的資料，這種 sampling 是垃圾不要用。
- random sampling（隨機抽樣）：隨機選擇 {{< math_inline >}}s%{{< /math_inline >}} 的資料，好方法。
- Stratified（分層抽樣）：先根據某個 feature 分層，每層選固定比例抽樣。例如，政府在調查國民所得時，將各家庭或調查單位依收入的多寡分成：高收入群、中收入層和低收入層，再依各層人數按同一比率抽取若干樣本進行調查。
- under-sampling & over-sampling：和分層抽樣不同，每層選的比例不一樣。主要是處理 imbalance problem（少數類別的資料量很大，但大部分類別的資料量很少）。
    - under-sampling：最小那組選 s，剩下其他組總共也選 s。
    - over-sampling：最大那組選 t，剩下其他組總共也選 t。