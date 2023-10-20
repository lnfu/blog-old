---
title: "[課程筆記] 機器學習概論（三） - 資料的品質"
date: 2023-10-03T15:39:50+08:00
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

[[課程筆記] 機器學習概論（一）](../2023-10-03-ml-01/)

[[課程筆記] 機器學習概論（二）](../2023-10-03-ml-02/)

[[課程筆記] 機器學習概論（三）](../2023-10-03-ml-03/)

[[課程筆記] 機器學習概論（四）](../2023-10-17-ml-04/)

---

這次講的是關於資料的品質。

data summary = data quality report

包含每個 feature 的統計測量值，像是平均數（Mean）、中位數（Median）、眾數（Mode）、標準差（Standard Deviation）、極值（最大/最小值），以及 data visualization（例如 histogram）。

![](./Screenshot%20from%202023-10-03%2015-42-59.png)

![](./Screenshot%20from%202023-10-03%2015-47-02.png)


# Data Quality Issues
anything unusual about the data in an ABT

常見的有：
1. missing values
2. irregular carginality（是指像是名字應該要不同卻有人一樣這種？）
3. 異常值（outliers）：例如某個值異常的大


## Missing Values



### 種類

#### MCAR = missing completely at random
此種缺失機制是假設缺失資料是隨機出現，與觀察到及未觀察到的資料皆無關。

受試者缺失資料的產生與本身健康狀態與試驗藥物沒有任何關係。例如受試者因為搬家的原因無法繼續參與試驗，或問卷未翻譯成該國語言導致受試者看不懂而無法回答。

#### MAR
缺失的發生和缺失的資料無關。或者說是缺失資料的發生與觀察到的資料有關，但是與未觀察到的資料無關。

消失的原因可以由其他 feature 推出來。

例如糖尿病臨床試驗，受試者若試驗期間空腹血糖值超過 270 mg/dl， 則須要退出試驗。

#### MNAR
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

