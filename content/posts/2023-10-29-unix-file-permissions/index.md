---
title: "UNIX 檔案權限"
date: 2023-10-29T16:25:56+08:00
draft: false
author: "Enfu Liao"
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

```
efliao@freebsd:~ $ ls -l
total 5
-rwxr-xr-x  1 efliao  wheel  2721 Oct 13 23:31 hw2.sh
```

`-rwxr-xr-x` 可以拆成四個部份：
1. 1st bit：檔案類型（第 1 個 bit）
2. 2nd - 4th bit：擁有者的權限
3. 5th - 7th bit：同群組使用者的權限
4. 8th - 10th bit：其他使用者的權限


# 檔案類型
|   | 說明                 |
|---|----------------------|
| - | 普通檔案             |
| d | 目錄                |
| b | block 設備檔案       |
| c | character 設備檔案   |
| l | 連結檔              |
| p | pipe 檔            |
| s | socket             |


# 檔案權限

對於普通檔案，權限的意義：
- `r`：讀取檔案 e.g., `cat`
- `w`：寫入檔案 e.g., `vim` 寫入、I/O redirect `>`、`>>`
- `x`：執行檔案 


對於目錄檔案：
- `r`：可以列出目錄的檔案和子目錄，但是不能得到檔案類型、長度、權限等，也不能讀取檔案 e.g., `ls`
- `w`：新增/刪除目錄中的檔案、重新命名目錄中的檔案 e.g., `cp`、`mv`
- `x`：進入目錄（`cd`）


如果要能得到目錄內檔案的類型、長度、權限等（`ls -l`），或是讀取（`cat`）目錄內的檔案，`r`、`x` 都要開啟。

除了 `rwx` 以外，還有三種比較特別的權限：
- setuid：對於一個可以執行的檔案，在 owner 的 `x` 位置填上 `s`，代表執行檔案時擁有 owner 的權限。
- setgid：對於一個可以執行的檔案，在 group 的 `x` 位置填上 `s`，代表執行檔案時擁有 group 的權限。
- sticky bit：對於一個目錄，在最末位的 `x` 填上 `s`，代表裡面的檔案只有檔案擁有者（如果沒特別改就是建立者）和 root 可以刪除。（`/tmp` 目錄）


