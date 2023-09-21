---
title: "[課程筆記] 計算機系統管理（SA）"
date: 2023-09-21T18:42:10+08:00
draft: false
author: "Enfu Liao"
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---



## FreeBSD 版本
```
---------------------- CURRENT (14.0 2022)

---------------------- STABLE 
        |
        \---RELEASE
```

```
FreeBSD-A.B.C-Type
```
- A: major version #
- B: minor version #
- C: slight patch version #
- Type: PRERELEASE, BETA, RC | RELEASE | STABLE | CURRENT

```sh
uname -r # 查看版本
freebsd-version # 查看版本
```

(`uname` 和 `freebsd-version` 的差異)[https://forums.freebsd.org/threads/freebsd-version-1-and-uname-1-different.70294/]


```sh
freebsd-update fetch install # 更新系統
```

### EoL（End-of-Life）
沒人在維護

RELEASE 三個月
magor # 五年（LTS）


## 安裝

[`adduser`](https://man.freebsd.org/cgi/man.cgi?adduser(8))


```
fetch https://nasa.cs.nctu.edu.tw/sa/2023/nasakey.pub
cat nasakey.pub >> /home/judge/.ssh/authorized_keys
ssh-keygen -l -f nasakey.pub
```


### 補充
- BIOS 在主機板上（firmware）
- (1982, IBM PC) 一個 sector = 512 Bytes
- 磁碟分割：MBR（4 partition）、GPT（128 partition）
- 檔案系統：UFS（Unix File System）、ZFS（推薦使用）、ext4（Linux 使用）
- partition = slice
- 只有輪子群組可以下 `su -`
- CST 是台灣時區
- `visudo` = `sudo vi /usr/local/etc/sudoers` + apply



```
gpart show
```

freebsd-boot：放 kernel
freebsd-zfs：一般檔案
freebsd-swap：virtual memory

`/dev` 代表 device
`/dev/ada0p1` 磁碟 partition 1
`/dev/ada0p2` 磁碟 partition 1
`/dev/ada0p3` 磁碟 partition 1



https://en.wikipedia.org/wiki/BSD_disklabel


ada: IDE, SATA
da: SCSI, usb stick


> 從主開機紀錄的結構可以知道，它僅僅包含一個64個位元組的硬碟分割區表。由於每個分割區資訊需要16個位元組，所以對於採用MBR型分割區結構的硬碟，最多只能辨識4個主要分割區（Primary partition）。（[維基百科：MBR](https://zh.wikipedia.org/zh-tw/%E4%B8%BB%E5%BC%95%E5%AF%BC%E8%AE%B0%E5%BD%95)）


只要 disk1 就夠了。


```
efliao@UX480FD:~$ sha
sha1sum       sha256sum     sha512sum     shasum        
sha224sum     sha384sum     shadowconfig  
```






## RAID = 磁碟陣列（多個硬碟）

Stripe：檔案分成多塊分別同時寫到多個硬碟


## 安裝軟體

### pkg（package）
Pre-built binary programs（已編譯）

### ports
source code

自己 compile

### 其他奇奇怪怪
tarball：包起來的東西（副檔名 .tar.gz）

tar = taps archive

vcs = version controll system e.g., git, svn