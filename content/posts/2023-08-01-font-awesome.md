---
title: "HTML 使用 Font Awesome 圖示"
date: 2023-08-01T13:45:22+08:00
draft: false
author: "Enfu Liao"
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

Font Awesome 提供很多好用的圖示（icon）。

## 使用

```html
<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.8.1/css/all.css" integrity="sha384-50oBUHEmvpQ+1lW4y57PTFmhCaXp0ML5d60M1M7uH2+nqUivzIebhndOJK28anvf" crossorigin="anonymous">
```

通常會使用在 `<i></i>` tag。例如 `<i class="fas fa-info-circle"></i>`。

> 可以到 https://fontawesome.com/icons 查詢要使用的 icon。

## 範例

```html
<div class="row">
    <div class="col">
        <button class="btn btn-block btn-primary"><i class="fas fa-thumbs-up"></i> Like</button>
    </div>
    <div class="col">
        <button class="btn btn-block btn-info"><i class="fas fa-info-circle"></i> Info</button>
    </div>
    <div class="col">
        <button class="btn btn-block btn-danger"><i class="fas fa-trash"></i> Delete</button>
    </div>
</div>
```