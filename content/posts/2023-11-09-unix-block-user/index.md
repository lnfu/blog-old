---
title: "UNIX 暫時停權使用者"
date: 2023-11-09T20:48:05+08:00
draft: false
author: "Enfu Liao"
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

可以改他的 Shell 成 `/sbin/nologin`，但是複權會要知道他的 Shell。

可以改他的密碼，但是複權會要知道他的密碼。

最好還是用 lock/unlock（會在她密碼前面加上 LOCK 之類的字樣）。