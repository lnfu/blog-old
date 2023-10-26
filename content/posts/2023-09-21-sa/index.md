---
title: "[課程筆記] 計算機系統管理（SA）"
date: 2023-09-21T18:42:10+08:00
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

課程網站：https://nasa.cs.nycu.edu.tw/sa/2023/

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

### ISO
可以到 nctu mirror 去下載，只要 disk1 就夠了，不用下載 dvd。


### 新增使用者
[`adduser`](https://man.freebsd.org/cgi/man.cgi?adduser(8))


### 磁碟分割
```
gpart show # 顯示 partition 資訊
```

freebsd-boot：放 kernel
freebsd-zfs：一般檔案
freebsd-swap：virtual memory

`/dev` 代表 device
`/dev/ada0p1` 磁碟 partition 1
`/dev/ada0p2` 磁碟 partition 1
`/dev/ada0p3` 磁碟 partition 1


https://en.wikipedia.org/wiki/BSD_disklabel


### 設定 judge 帳號免密碼登入
```
fetch https://nasa.cs.nctu.edu.tw/sa/2023/nasakey.pub
cat nasakey.pub >> /home/judge/.ssh/authorized_keys
ssh-keygen -l -f nasakey.pub
```

### 修改 sudo 權限

```sh
visudo # 等同 sudo vi /usr/local/etc/sudoers + 套用設定
sudo update-alternatives --config editor # 更改 visudo editor
```


### 補充
- BIOS 是在主機板上（firmware）
- (1982, IBM PC 規格延續到現在) 一個 sector = 512 Bytes
- 磁碟分割：MBR（4 partition）、GPT（128 partition）
- 檔案系統：UFS（Unix File System）、ZFS（推薦使用）、ext4（Linux 使用）
- partition = slice
- 只有輪子群組可以下 `su -`
- CST 指的是台灣時區
- `visudo` = `sudo vi /usr/local/etc/sudoers` + apply
- 如果 ping 的延遲很低，ssh 卻很慢可能是 DNS 反解問題。可以參考：https://www.ltsplus.com/linux/sshd-login-slow
        - config 加入 `UseDNS no`


ada: IDE, SATA
da: SCSI, usb stick


> 從主開機紀錄的結構可以知道，它僅僅包含一個64個位元組的硬碟分割區表。由於每個分割區資訊需要16個位元組，所以對於採用MBR型分割區結構的硬碟，最多只能辨識4個主要分割區（Primary partition）。（[維基百科：MBR](https://zh.wikipedia.org/zh-tw/%E4%B8%BB%E5%BC%95%E5%AF%BC%E8%AE%B0%E5%BD%95)）



### RAID = 磁碟陣列（多個硬碟）

Stripe：檔案分成多塊分別同時寫到多個硬碟

## 安裝軟體

### pkg（package）
Pre-built binary programs（已編譯）

```sh
pkg search vim
pkg install vim
pkg upgrade vim
pkg delete vim
```

```sh
pkg update # 更新 database
pkg info # 查看已安裝的軟體
pkg version -v 
```

### ports
source code

自己 compile

### 其他奇奇怪怪
tarball：包起來的東西（副檔名 .tar.gz）

tar = tape archive

vcs = version controll system e.g., git, svn



### Ubuntu Issues
[ZFS installer option](https://www.reddit.com/r/zfs/comments/gwk6dj/creating_ubuntu_server_with_zfs/)
[刪除 Desktop 相關套件](https://askubuntu.com/questions/484095/how-do-i-remove-all-desktop-related-packages-leaving-server-only)

`wget` instead of `fetche`







# yq for yaml, jq for json




# date -Iseconds -r 1696164685
TZ=America/Los_Angeles date -Iseconds -r 1696164685


# echo -n Hello
不要有結尾空行

# `` vs $()
https://stackoverflow.com/questions/4708549/what-is-the-difference-between-command-and-command-in-shell-programming


# pw groupadd [name]
建立群組

# pw groupmod [name] -m [user]
使用者加入群組

# sudo ntpdate tock.stdtime.gov.tw
	tock.stdtime.gov.tw
	watch.stdtime.gov.tw
	time.stdtime.gov.tw
	clock.stdtime.gov.tw	
	tick.stdtime.gov.tw

https://www.stdtime.gov.tw/chinese/bulletin/NTP%20promo.txt

2023-10-13T14:03:33+08:00
2023-10-01T20:51:25:+0800



# date -Iseconds -j -f "%s" 1664447738
unix time to TA's format


---

2023.10.19


port vs. pkg

pkg -> binary

port -> source code

ports tree 位置：`/usr/ports`

```
/usr/ports/<category>/<name>
/usr/ports/editors/vim
```

fetch, extract, update, cron

corn 有點像是行事曆（定期做事）

`portsnap`: 更新 ports tree

```
sudo portsnap fetch extract update
```

```
# 搜尋 port
cd /usr/ports
sudo make fetchindex
make search name=<program-name>
make search key=<string>

---

psearch vim # 比上面簡單好用
```

```
# 安裝軟體
sudo make install clean
```





如果剛安裝完應用程式，但是找不到時（現在好像都不會遇到了）：
```
rehash
```

在 FreeBSD 系统安装以后的默认设置中，`/etc/make.conf` 这个文件并不存在，你可以自己建立一个。
```
# 本地 mirror site
# /etc/make.conf
MASTER_SITE_BACKUP?= \
	http://FreeBSD.cs.nctu.edu.tw/distfiles/${DIST_SUBDIR}/
MASTER_SITE_OVERRIDE?= ${MASTER_SITE_BACKUP}
```



請不要 "curl pipe bash"，非常危險！



# Shell

- Bourne Shell *by S.R. Bourne*: `/bin/sh` 最早
	- C Shell *by Bill Joy*: `/bin/csh`
		- TENEX C Shell *by Ken Greer*: `/bin/tcsh`
		- Z Shell
	- Bourne-Again Shell
	- Korn Shell

> vi 也是 Bill Joy 寫的（Sun 公司也是她開的 牛B）

## Startup Files
- `/etc/profile`
- `~/.profile`


---


```
efliao@UX480FD:~$ ps -p $$
    PID TTY          TIME CMD
   8411 pts/4    00:00:00 bash
```