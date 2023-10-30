---
title: "安裝 `sudo`"
date: 2023-10-30T11:54:37+08:00
draft: false
author: "Enfu Liao"
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

FreeBSD 剛安裝完後預設是還沒有 `sudo` 的，以下說明如何在 FreeBSD 安裝 `sudo` 程式。

```
ping -c4 8.8.8.8            # 確認網路
su -                        # 切換成 root
pkg install sudo            # 安裝 sudo
```

安裝完後使用者還不能使用 `sudo`，我們需要編輯 `/etc/sudoers` 檔案。

在編輯之前，可以先更改一下預設的文字編輯器。

```
setenv EDITOR vim           # 將預設開啟的文字編輯器設定為 vim
```

如果想把系統所有使用者預設的文字編輯器都設定成 vim 可以用

```
sudo update-alternatives --config editor
```

她會列出所有可以的文字編輯器，輸入對應的編號即可。

```
visudo                      
```

然後把加入使用者或是群組：
```
efliao ALL=(ALL) ALL        # 使用者 efliao
%admin ALL=(ALL) ALL        # 群組 admin
```

