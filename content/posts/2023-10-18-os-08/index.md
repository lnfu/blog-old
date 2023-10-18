---
title: "[課程筆記] 作業系統概論（八） - 同步（Synchronization）"
date: 2023-10-18T10:16:54+08:00
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

這章老師說會比較難><

:-D

Sync 不能不做（程式正確性問題）。

# 用 Counter

```
// producer
while (true) {
    while (count == BUFFER_SIZE)
        ;
    buffer[in] = nextProduced;
    in = (in + 1) % BUFFER_SIZE;
    count++;
}
```

```
// consumer
while (true) {
    while (count == 0)
        ;
    nextConsumed = buffer[out];
    out = (out + 1) % BUFFER_SIZE;
    count--;
}
```

`count++` 在 C 語言看起來只有一行，但是轉成指令會是很多行（有可能是以下三行，取決於 CPU 架構）。

`count++` 不是 atomic operation（atomic operation 指的是不能再被分割）。

```
reg1 = count
reg1 = reg1 + 1
count = reg1
```



