---
title: "[課程筆記] 演算法概論（八） - 背包問題"
date: 2023-10-31T11:36:36+08:00
draft: false
author: "Enfu Liao"
tags: ["課程筆記", "演算法"]

# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

有 n 種物品，每種物品不同的重量（weight）和價值（value）。

你的背包總共能容納的重量是 W，要裝總價值最高。

DP or Greedy?

# 物品不可切割

## 0/1 背包問題

對於一種物品，要麼選她（只能選一次），要麼不選。

可以用 DP 來解。

```
T[i, j] 表示目前背包容量是 j，考慮過第 i 個物品後背包的總價值。

if (w[i] > j) {
    // 已經放不下了
    T[i, j] = T[i-1, j]
}
else {
    // 考慮是否要放第 i 個物品
    T[i, j] = max(T[i-1, j], T[i-1,j-w[i]] + v[i]) // 不放 or 放
}

最後求 T[n, W]
```

時間複雜度：O(n W)


# 物品可以切割（fractional）

