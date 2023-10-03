---
title: "Linux 相關小知識"
date: 2023-08-03T15:55:52+08:00
draft: true
author: "Enfu Liao"
tags: ["Linux"]

# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

# tar
將多個文件打包（群組）成一個。

沒有壓縮，所以通常會搭配 gzip 一起使用來壓縮檔案。

打包並壓縮後的檔案副檔名會是 `.tgz` 或是 `.tar.gz`。

流程如下：

```
file --(tar)--> file.tar --(gzip)--> file.tar.gz
```

解壓縮：
```
tar xvzf file.tgz
```

# xargs
有點難解釋，忘記就看[這篇](https://blog.gtwang.org/linux/xargs-command-examples-in-linux-unix/)吧，寫得蠻清楚的。

# 更改預設 terminal

```bash
sudo update-alternatives --install /usr/bin/x-terminal-emulator x-terminal-emulator /usr/local/bin/alacritty 200
sudo update-alternatives --config x-terminal-emulator
```

https://unix.stackexchange.com/questions/726980/st-simple-terminal-doesnt-show-up-when-selecting-alternative-x-terminal-emula
https://documentation.suse.com/zh-tw/sles/15-GA/html/SLES-all/cha-update-alternative.html