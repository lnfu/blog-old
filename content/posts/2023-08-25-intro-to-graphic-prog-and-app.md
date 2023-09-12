---
title: "繪圖程式設計與應用 課程筆記"
date: 2023-09-12T00:20:21+08:00
draft: false
author: "Enfu Liao"
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

這是隔壁的課，不知道可不可以承認...

## 2023-08-25
這門課主要是講解如何使用 OpenGL。（理論的東西好像是在另一門課）

這禮拜先講了 Transformation 和座標系等簡單的數學。

## Transformation
我們這邊討論的 transformation 就是指在 3D 座標下的幾何轉換。

我們會用 homogeneous coordinate 來表示點和向量（w = 1 是點；w = 0 是向量）。

OpenGL 有三個重要的矩陣：MVP matrices（model, view, projection）。

![](https://developer.download.nvidia.com/CgTutorial/elementLinks/fig4_1.jpg)

> OpenGL 的矩陣是 column major order。
![](https://upload.wikimedia.org/wikipedia/commons/thumb/4/4d/Row_and_column_major_order.svg/1200px-Row_and_column_major_order.svg.png)

常見的矩陣有：
1. identity（單位矩陣）：沒有任何作用
2. translation（位移）
3. scaling（縮放）：對向量來說，如果不是等比例的縮放會改變方向（寫程式時要注意）
4. rotation（旋轉）：剛體。對於尤拉角（Euler Angle）表示，可能會導致所謂的 **gimbal lock**，解決方式是引入 **quaternion**。（有點複雜，之後研究）

https://youtu.be/zjMuIxRvygQ


### Projection
這門課只介紹了兩種投影：orthographic 和 perspective。

> perspective 的那個斷頭角錐稱作 **frustum**

![](https://i.stack.imgur.com/q1SNB.png)

比較需要注意的是，不要因為方便就把 far plane 設定超級大，這樣會因為 near plane 和 far plane 距離過大導致壓縮後太靠近加上精度問題就會有 z fighting 的問題。

![](https://www.gameislearning.url.tw/pic/my/zBuffer03.jpg)

在 OpenGL 中，projection 的階段還會把座標從右手座標（RHC）轉成左手座標（LHC），最終結果會是一個 [-1, 1][-1, 1][-1, 1] 的立方體。

### 結論＆作業
實際在撰寫程式時，我們會在主程式預先算好 MVP 矩陣，在 GLSL 對每個點去乘這個矩陣，結果放到 gl_Position 變數以描述所有點在 clip space 的位置。

* 推導 orthographic 和 perspective projection 的矩陣
* 推導 view matrix
