---
title: "Node.js 和 PostgreSQL 的小技巧"
date: 2023-08-25T00:47:48+08:00
draft: false
author: "Enfu Liao"
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

平常在進行後端開發時，我們通常會在本地開一個資料庫（的容器）做測試，這篇主要是介紹一個可以比較快速且有邏輯建立好整個資料庫作為開發使用，並且透過所謂的 migration 以及腳本化達到重複使用（reuse）。

## 正題

先到 https://dbdiagram.io/ 建立 Diagram，匯出 PDF 和 SQL 檔案。

> 語法請參考 dbdiagram 官方說明文件。

其中 PDF 檔案可以在和別人討論資料庫設計時使用（或是也可以匯出成圖片）；SQL 檔案在之後的 migration 會用到。

使用 `node-pg-migrate` 輕鬆管理開發時本地資料庫的表格（migration）。

```
yarn add pg
yarn add node-pg-migrate
yarn node-pg-migrate create init
```

這樣操作完後會建立一個 `migrations/xxxxxxxxxxxxx_init.js` 檔案。

然後就可以把一開始匯出的 SQL 檔案內容貼到 migrate up 的部份，並且在 migrate down 撰寫好 drop table（記得順序要和 create table 顛倒以解決表格間衝突的問題）。

記得設定 `package.json` 的 scirpt 簡化所有操作。

```
  "scripts": {
    "dev": "node app.js",
    "postgres": "sudo docker run --name postgres15 -p 5432:5432 -e POSTGRES_USER=root -e POSTGRES_PASSWORD=secret -d postgres:15-alpine",
    "createdb": "sudo docker exec postgres15 createdb --username=root --owner=root note",
    "dropdb": "sudo docker exec postgres15 dropdb note",
    "migrate": "DATABASE_URL=postgres://root:secret@localhost:5432/note node-pg-migrate"
  }
```

另外 SQL Client 推薦 open source 的軟體 DBeaver，雖然目前用下來好像 tableplus（非 open source）比較好用...

