---
title: "Hello, Hugo"
date: 2023-07-25T22:24:39+08:00
draft: false
author: "Enfu Liao"
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

Hugo 是以 go 語言開發的靜態網頁生成器/框架，特色是速度非常快。

# 安裝 Hugo

那至於要如何安裝 Hugo 呢，因為我的電腦是 Ubuntu 22.04，可以直接用 apt 來安裝。
```
apt install hugo
```

# 建立網站
在 Hugo 建立網站的方式也很簡單。
```
hugo new site [path]
```
由於我想使用 GitHub Pages 託管我的靜態網站，所以我會先建立一個 GitHub Repo，然後 clone 到本地在建立網站。
```
cd [path]
hugo new site . --force
```
> 使用 `--force` 的原因是因為目錄不是空的（有 README.md、LICENSE 文件）

建立好後可以看到多出了許多目錄和檔案。我們可以先去下載自己喜歡的主題到 `themes/` 中，然後在設定檔 `config.toml` 套用主題。
```toml
# config.toml
theme = "[主題名稱]"
```

# 撰寫文章
建立文章（網頁）的方式一樣是 `hugo new`。
```
hugo new [檔名]
```
建立好的檔案會存放在 `content/` 中，習慣上我們會把文章放在叫做 `posts/`（或是 `blog/`）的子目錄下方便管理。
```
hugo new posts/[檔名]
```
我們會發現建立的檔案預設就有一些資訊，這是因為 Hugo 建立文章是把 `archetypes/` 下的特定文件（e.g., `default.md`）作為範本。
>我一般會讓檔名符合像是 **2023-07-25-my-post.md** 的格式。這樣在排序時就會依照日期，只使用 hyphen 和小寫字母比較不會出錯。

# 執行
在本地執行網站。
```
hugo server
```