---
title: "[課程筆記] 作業系統概論（四）"
date: 2023-10-08T18:01:29+08:00
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

scheduling 

process（PCB）的 queue

scheduling 的過程（多條可能路線）
![](./Screenshot%20from%202023-10-08%2018-05-20.png)

# scheduler 種類
## short-term
非常頻繁的使用，所以要快，overhead 要盡可能到最小 -> 用組合語言撰寫

ready queue 實際上不是像圖片那樣用 linear 方式儲存，因為這樣會太慢，以 Linux 為例是用 RB Tree（紅黑樹）。

e.g., CPU scheduler

CPU scheduler 有 mechanism（也就是 context switch）也有 policy（該選誰來 running）。

## long-term
決定哪些 process 要載入到記憶體。

process 可以分成 I/O-bound 和 CPU-bound
- I/O-bound：e.g., 文字編輯器（運算量不大，主要都是在做 I/O 處理）
- CPU-bound：e.g., 遊戲、影片轉檔、圖像程式

long-term scheduler 可以去分配平衡 I/O-bound 和 CPU-bound process 提高效率。

但是基本上 time sharing 的系統（例如我們在用的桌上型電腦）都不會有 long-term scheduler。

## medium-term

