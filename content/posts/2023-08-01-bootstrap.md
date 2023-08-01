---
title: "Bootstrap"
date: 2023-08-01T12:01:48+08:00
draft: false
author: "Enfu Liao"
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

Bootstrap 是目前最受歡迎的 CSS framework（雖然近年來前端領域好像都在用 tailwind CSS）。

## 使用

加入這行到 HTML 即可使用 Bootstrap。

```html
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous"/>
```

把所有元素放到 `<div class="container-fluid"></div>`。

> 除了 `container-fluid` 以外還有像是 `container`、`container-md`、...詳情請洽官方文檔說明

## 照片自動調整成符合視窗大小

```html
<img class="img-responsive" src="">
```

## 文字樣式

```html
<h2 class="text-primary text-center">your text</h2>
```

如果要在內文強調可以用 `<span class="text-primary></span>`。

## 按鈕

```html
<button class="btn btn-default">Like</button>
```

所有按鈕都要有 `btn` attribute。`btn-default` 是按鈕的一種樣式（白底），也可使用 `btn-primary`（網站的主色彩）、`btn-info`、`btn-danger`（常用於 Delete）樣式。

> `btn-default` 好像在 Bootstrap 5 被拿掉了。

按鈕預設大小是包住按鈕中的文字，加上 `btn-block` attribute 就會變成 100% 的大小。

## 格線系統
Bootstrap 的（響應式）格線系統有 12 欄。

```
col-md-4 => median e.g., laptop
col-xs-4 => extra small e.g., mobile phone
```

```html
<div class="container">
    <div class="row">
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
    </div>
</div>
```

