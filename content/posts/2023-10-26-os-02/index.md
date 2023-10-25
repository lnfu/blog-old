---
title: "[課程筆記] 作業系統概論（二） - 架構設計"
date: 2023-10-26T02:14:42+08:00
draft: false
author: "Enfu Liao"
tags: ["課程筆記", "作業系統"]

# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---


一個作業系統會提供以下服務：
1. system programs：e.g., `ls`, `cp`, `rm`
2. interface（shell）
3. system calls

shell 可以再兩種：
1. CLI：命令行
2. GUI：圖形

> 注意，`cd` 並不是 system programs，而是 shell 內建就有的指令。

補充：WYSIWYG editors 只得是文字編輯器滿足 what you see is what you get e.g., Word

![](./Screenshot%20from%202023-10-02%2012-44-22.png)

APIs: POSIX API for UNIX, Win32 API for Windows, Java API for JVM

# MS-DOS
- 簡單、沒有結構
- 沒有 protection（程式可以直接 access hardware）
- 一次只能執行一個程式

![MS-DOS Structure](./Screenshot%20from%202023-09-27%2010-26-51.png)

> [TSR = Terminate-and-stay-resident program](https://zh.wikipedia.org/zh-tw/%E7%BB%88%E6%AD%A2%E5%8F%8A%E5%B8%B8%E9%A9%BB%E7%A8%8B%E5%BC%8F)

# UNIX（Monolithic kernel；單體式、整合式核心）
和系統程式有關的都在 kernel 執行（有效率）

![UNIX Structure](./Screenshot%20from%202023-09-27%2010-32-53.png)

> 以前買一個印表機要安裝 driver 時，必須要有 kernel source code 和印表機 driver 的 source code 才能擴充（重新編譯 kernel）=> 不方便 => Modules 的概念出現

kernel module 範例：
參考[[Linux Kernel] 撰寫簡單 Hello, World module (part 1).
](https://blog.wu-boy.com/2010/06/linux-kernel-driver-%E6%92%B0%E5%AF%AB%E7%B0%A1%E5%96%AE-hello-world-module-part-1/)
```c
#include <linux/kernel.h> /* header file for structure pr_info */
#include <linux/init.h>
#include <linux/module.h> /* header file for all modules */
#include <linux/version.h>

MODULE_DESCRIPTION("Hello World !!");
MODULE_AUTHOR("Enfu Liao");
MODULE_LICENSE("GPL");

static int __init hello_init(void)
{
    pr_info("Hello, world\n");
    pr_info("The process is \"%s\" (pid %i)\n", current->comm, current->pid);
    return 0;
}

static void __exit hello_exit(void)
{
    printk(KERN_INFO "Goodbye\n");
}

module_init(hello_init);
module_exit(hello_exit);
```

```makefile
obj-m := hello.o

KERNELDIR ?= /lib/modules/$(shell uname -r)/build

all default: modules
install: modules_install

modules modules_install help clean:
    $(MAKE) -C $(KERNELDIR) M=$(shell pwd) $@
```

> 看起來一般程式沒什麼不同，不過可以去做一些 kernel 才能做的事情。（不過要是 root 才能 insert/remove module）

```sh
insmod ./hello.ko # 載入 module
rmmod  ./hello.ko # 移除 module
```


# Microkernel

一些基本的留在 kernel（如下），其他的東西放到 user space
- CPU schedule
- IPC

https://unix.stackexchange.com/questions/197918/are-kernel-mode-and-user-mode-hardware-features-or-software-features


優點（和單體式比較）：
1. 更好 extend
2. 更安全（kernel 更小、更不容易進到 kernel mode）
3. 移植比較簡單
4. robust

缺點：
1. performance 比較差。使用場景大多是嵌入式系統（效能相比安全性等非主要考量）

參考：https://en.wikipedia.org/wiki/Tanenbaum%E2%80%93Torvalds_debate

> INTEL ME（Intel Management Engine）有一個 MINIX（買 CPU 送你作業系統:D）


Google Fuchsia 是 Microkernel，客群是 IoT devices。
UI：Flutter (UI framework) + Dart (language) 
system services and IPC：Fuchsia
microkernel：Zircon


# Mac OS X
變形 Microkernel

![](./Screenshot%20from%202023-09-27%2011-05-45.png)

> Steve Jobs 在 NeXT 開發，之後帶回到 Apple。




# 虛擬機
虛擬機跑在 user mode（就是一個 process）
![](./virtual-machine.jpg)

system call（interrupt）：
![interrupt](./virtual-machine-interrupt.jpg)


## Java 虛擬機（JVM）
> non-native execution

> Android - ART vs Dalvik
> Approach: ART uses AOT(Ahead Of Time) approach and compiles the whole code during the installation time but the Dalvik uses JIT(Just In Time) approach and complies only a part of the code during installation and rest of the code will be compiled dynamically.

> https://en.wikipedia.org/wiki/Acquisition_of_Sun_Microsystems_by_Oracle_Corporation


# Mechanism vs. Policy

Mechanism 不做決策

Policy 有決策（不會影響結果是否正確，主要影響效能問題）


# Process（行程）
process = job = task = a program **in execution**
- text
- stack：local variable
- heap：全域變數
- data
- program counter, CPU registers

![](./Screenshot%20from%202023-09-27%2011-57-56.png)


--> 2023-09.27

