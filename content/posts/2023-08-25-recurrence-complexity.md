---
title: "Complexity of Recurrence Relation"
date: 2023-09-12T09:38:21+08:00
draft: false
author: "Enfu Liao"
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---




![](https://slideplayer.com/7351407/24/images/slide_1.jpg)

以 merge sort 舉例，每個 sorting problem 可以拆成兩個 sorting problem，其花費的時間是原本的一半，所以 a = 2、b = 2，另外拆開（divide）的時間只需要 O(1)，合併（combine）則需要迴圈跑過一次所以是 O(n)。

要解出 recurrence 複雜度的一般式有兩個方法：
1. backward substitution
2. Master Theorem

## backward substitution
其實就是數學歸納法。畫出一棵樹（葉子是 base condition）


## Master Theorem


## 參考
* https://mycollegenotebook.medium.com/%E6%99%82%E9%96%93%E8%A4%87%E9%9B%9C%E5%BA%A6-%E9%81%9E%E8%BF%B4-%E4%B8%8A-f6d51a462394
* https://mycollegenotebook.medium.com/%E6%99%82%E9%96%93%E8%A4%87%E9%9B%9C%E5%BA%A6-%E9%81%9E%E8%BF%B4-%E4%B8%8B-master-th-307ad4608ab6