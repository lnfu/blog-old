---
title: "[課程筆記] 作業系統概論（十） - 記憶體"
date: 2023-11-08T10:22:06+08:00
draft: false
author: "Enfu Liao"
tags: ["課程筆記", "作業系統"]
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

# Address Binding

可以在三個階段做 Address Binding：
1. compile time: 在編譯時期就決定好 address（absolute addressing），缺點是如果有兩個程式位置相同就會有 conflict，所以不能同時載入這兩個程式。
2. load time: 和 PC 的相對位置，PC-relative addressings。（實際在算的時候 PC 已經 + 2，所以應該是 PC + 2 - n）
3. execution time: 需要 MMU 硬體支持（才夠快）

![execution time address binding](./Screenshot%20from%202023-11-08%2010-47-10.png)

## Logical vs Physical

logical address: CPU 以為的位置
physical address: 實際在記憶體的位置

## MMU
memory management unit

一定是硬體，軟體做不了（太慢）

功能有：
1. relocation（剛剛提的）
2. paging
3. segmentation

![](./Screenshot%20from%202023-11-08%2010-49-56.png)
