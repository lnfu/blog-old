---
title: "[課程筆記] 作業系統概論（八） - 同步（Synchronization）"
date: 2023-10-18T10:16:54+08:00
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

這章老師說會比較難><

:-D

Sync 不能不做（程式正確性問題）。

# Race Condition

以 counter 為例子，我們前面用 counter 去計算 buffer 裡面有多少個，但是這其實會產生 race condition 的問題。

```c
// producer
while (TRUE) {
    while (count == BUFFER_SIZE)
        ;
    buffer[in] = nextProduced;
    in = (in + 1) % BUFFER_SIZE;
    count++;
}
```

```c
// consumer
while (TRUE) {
    while (count == 0)
        ;
    nextConsumed = buffer[out];
    out = (out + 1) % BUFFER_SIZE;
    count--;
}
```

`count++` 在 C 語言看起來只有一行，但是轉成指令會是很多行（有可能是以下三行，取決於 CPU 架構）。

`count++` 不是 atomic operation（atomic operation 指的是不能再被分割）。

```
# assembly code of count++
reg1 = count
reg1 = reg1 + 1
count = reg1
```

```
# assembly code of count--
reg1 = count
reg1 = reg1 + 1
count = reg1
```

假設有 `count++` 和 `count--` 同時發生，他們的指令可能會互相參差，造成結果不同（三種結果只有一個是對的）。

這時候可能會想到用一個 `safe` 變數去紀錄是否有其他的 thread 在讀/寫 `count`。

```
// thread 1
if (safe == TRUE)
    safe = FALSE;
    // do something...
```

不過因為 `safe == TRUE` 和 `safe = FALSE` 也不是 atomic，所以問題還是沒解決。


另一個 race condition 的例子（linked list 插入）：
![](Screenshot%20from%202023-10-18%2010-42-05.png)

總結來說，race condition 的主要原因就是因為 operation 不是 atomic。

## 解決

hardware-based approaches（底層實做）

### disable/enable interrupt

在要進行 critical section（會發生 race condition 的部份），之前先 disable interrupt，等到執行完在 enable interrupt（在 kernel mode 開啟/關閉 interrupt）。

```
do {
    disable interrupt; // entry section
    // critical section
    enable interrupt; // exit section
    
    ...

} while (TRUE)
```

不過這方法在多處理器（multiprocessor）的環境下是沒有用的（因為你還是沒辦法阻止其他 CPU 去動到資料）。

### 包裝成 atomic instruction

使用 TAS 指令：

```
// TAS（在這邊的程式碼只是表示，在硬體已經實做出來，並非是一個 C 語言函數）
boolean TestAndSet(boolean *target)
{
    boolean rv = *target;
    *target = TRUE;
    return rv;
}

// Solution（使用 TAS 指令解決 race condition 的 C 程式）
do {
    while (TestAndSet(&lock))
        ;

    // critical section
    lock = FALSE;
    
    ...
} while(TRUE);
```

使用 Swap 指令：

```
// Swap（在這邊的程式碼只是表示，在硬體已經實做出來，並非是一個 C 語言函數）
void Swap(boolean *a, boolean *b)
{
    boolean temp = *a;
    *a = *b;
    *b = temp:
}

// Solution（使用 Swap 指令解決 race condition 的 C 程式）
do {
    key = TRUE;
    while(key == TRUE)
         Swap(&lock, &key);

    // critical section
    lock = FALSE;
    
    ...
} while (TRUE);
```

Spin Lock：

```x86asm
; Intel syntax

locked:                      ; The lock variable. 1 = locked, 0 = unlocked.
     dd      0

spin_lock:
     mov     eax, 1          ; Set the EAX register to 1.

     xchg    eax, [locked]   ; Atomically swap the EAX register with
                             ;  the lock variable.
                             ; This will always store 1 to the lock, leaving
                             ;  the previous value in the EAX register.

     test    eax, eax        ; Test EAX with itself. Among other things, this will
                             ;  set the processor's Zero Flag if EAX is 0.
                             ; If EAX is 0, then the lock was unlocked and
                             ;  we just locked it.
                             ; Otherwise, EAX is 1 and we didn't acquire the lock.

     jnz     spin_lock       ; Jump back to the MOV instruction if the Zero Flag is
                             ;  not set; the lock was previously locked, and so
                             ; we need to spin until it becomes unlocked.

     ret                     ; The lock has been acquired, return to the calling
                             ;  function.

spin_unlock:
     mov     eax, 0          ; Set the EAX register to 0.

     xchg    eax, [locked]   ; Atomically swap the EAX register with
                             ;  the lock variable.

     ret                     ; The lock has been released.
```
