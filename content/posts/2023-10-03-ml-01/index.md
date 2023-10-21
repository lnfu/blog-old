---
title: "[課程筆記] 機器學習概論（一） - 機器學習簡介"
date: 2023-10-03T13:29:39+08:00
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

[[課程筆記] 機器學習概論（三）](../2023-10-03-ml-03/)

[[課程筆記] 機器學習概論（四）](../2023-10-17-ml-04/)

---

放棄聽講，還是靠自己讀投影片好了...

第一份投影片是 introduction。

**機器學習（Machine Learning）**：an automatic process that extracts patterns from data (or past experiences)

為什麼我們需要機器學習呢？為了補足人腦的缺陷，我們在生活中常常會遇到許多問題是
1. 專家不存在
2. 專家無法解釋
3. 解法一直在變
4. 需要處理特定條件

這時候可能就需要依靠機器學習來幫助我們在未來時做出決策。

不過，機器學習得到的這些 model 只是 approximation（近似），不能百分之百代表真實世界。

**Consistent Model**：訓練出來的模型在 dataset 的表現穩定一致。

淺白理解：把 dataset 的每一筆的 descriptive features 放進模型得到的結果和 target feature 相同。（好像不一定是完全相同，只要 MSE 之類的夠小就算？）

> There is more than one candidate consistent model.

另外，dataset 的資料會有 noise，模型應該盡可能不被這些 noise 影響。

**Sample Bias**：因為缺乏代表性的樣本（沒有辦法代表整體）所產生的誤差，例如從一個班上都只有抽樣到男生。我們應該盡可能消除 sample bias。

**Inductive Bias**：在機器學習演算法中所作的假設（的集合）。（例如假設模型是一個線性方程式）

如果做錯 inductive bias 可能會得到兩種情況：
1. **Underfitting**
2. **Overfitting**

![underfitting & overfitting](./Screenshot%20from%202023-10-03%2014-45-24.png)

# 機器學習的種類

## 監督式學習（Supervised Learning）
資料會 label（也就是 target feature）。

根據過去的資料（input & output）建立 rule，如此就能透過之後的 input 預測出 output。

- input：a set of **descriptive features**
- output：**target feature**

> 注意這邊的用語（descriptive features & target feature）

階段一：利用機器學習相關的演算法從 dataset 訓練出模型（model）

階段二：根據 query instance 放到模型中得到預測結果

像是告訴小孩這個東西的長寬高、形狀、色彩（descriptive features），然後跟小孩說這是桌子或是這是椅子（target feature）。

### 應用
- 分類（classification）
- 迴歸（Regression）


## 非監督式學習（Unsupervised Learning）
資料不用 label（沒有 target feature）。

像是給小孩看所有動物照片，讓她自己去發現不同、分類。


## 強化式學習（Reinforcement Learning）
學習 policy（a sequence of actions）。

不會有像是 supervised 一樣當下就有所謂的 target feature，但是會有 reward。

像是下圍棋，每一步的當下不會告訴她這是對還是錯，但是結束後會有輸贏（負向或正向回饋）。

### 應用
- AlphaGo（下圍棋）
- 走迷宮