---
title: "[課程筆記] 演算法概論（二） - 分治法"
date: 2023-10-17T10:43:18+08:00
draft: false
author: "Enfu Liao"
math: true
tags: ["課程筆記", "演算法"]
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---


---


分治法（divide and conquer）


# 求解遞迴式的時間複雜度

![](https://slideplayer.com/7351407/24/images/slide_1.jpg)

以 merge sort 舉例，每個 sorting problem 可以拆成兩個 sorting problem，其花費的時間是原本的一半，所以 a = 2、b = 2，另外拆開（divide）的時間只需要 O(1)，合併（combine）則需要迴圈跑過一次所以是 O(n)。

要解出 recurrence 複雜度的一般式有兩個方法：
1. backward substitution
2. Master Theorem

## substitution method
用數學歸納法（strong induction）證明。

> 可以先用 recursion-tree 去分析



## Master Theorem

這個方法只能用來求解以下形式的遞迴問題，不過因為算起來很簡單所以很好用。
{{< math_block >}}
T(n) = a * T(n / b) + f(n)
a > 0
b > 1
n \in \mathbb{N}
{{< /math_block >}}

其中，{{< math_inline >}}f(n){{< /math_inline >}} 是每一次分割和合併所花的時間，{{< math_inline >}}a{{< /math_inline >}} 是分割的子問題數量，且每個自問題大小是原本問題的 {{< math_inline >}}1/b{{< /math_inline >}}。

Master Theorem 分成三種 case:
### 由分割後的子問題決定（{{< math_inline >}}T(n / b){{< /math_inline >}}）

也就是 {{< math_inline >}}f(n){{< /math_inline >}} 比 {{< math_inline >}}T(n / b){{< /math_inline >}} 小一點。

{{< math_block >}}

\epsilon \gt 0,
f(n) = O(n^{log_b{a - \epsilon}})

\implies T(n) = \Theta (n^{log_{b}a})

{{< /math_block >}}


### 由當次分割合併決定（{{< math_inline >}}f(n){{< /math_inline >}}）

也就是 {{< math_inline >}}f(n){{< /math_inline >}} 比 {{< math_inline >}}T(n / b){{< /math_inline >}} 大一點。

{{< math_block >}}

\epsilon \gt 0,
f(n) = \Omega(n^{log_b{a + \epsilon}})

\implies T(n) = \Theta (f(n))

{{< /math_block >}}

### 共同決定

{{< math_inline >}}f(n){{< /math_inline >}} 比 {{< math_inline >}}T(n){{< /math_inline >}} 幾乎一樣（可以稍微多一點）。

{{< math_block >}}

k \ge 0,
f(n) = \Theta(n^{log_b{a} lg^{k}n})

\implies T(n) = \Theta (n^{log_{b}a} lg^{k+1}n)

{{< /math_block >}}

第三種常見的情況是 {{< math_inline >}}k = 0{{< /math_inline >}}，此時 {{< math_inline >}}T(n) = \Theta (n^{log_{b}a lg n}{{< /math_inline >}}

### 補充
以下是 {{< math_inline >}}f(n) = cn{{< /math_inline >}} 的情況：
![](https://cdn-images-1.medium.com/max/1200/0*SMhJVzBPbBuiGOws.png)


# 分治法例子
## 合併排序（merge sort）
1. 每次對左右兩半繼續跑合併排序（遞迴）
2. 排序完左右後再用 {{< math_inline >}}T(n) = n{{< /math_inline >}} 跑過一次（每次比較兩邊的元素誰先誰後）

遞迴式：{{< math_inline >}}T(n) = 2T(n/2) + \Theta(n){{< /math_inline >}}

合併排序是穩定排序

平均、最好、最壞都是 {{< math_inline >}}O(n lg(n)){{< /math_inline >}}

最壞的情況（兩邊比較時每次都是一左一右）：
```
Input array arr[] = [4,0,6,2,5,1,7,3]
                           /  \
                          /    \
                  [4,0,6,2] and [5,1,7,3]
                     / \           / \
                    /   \         /   \
                 [4,0] [6,2]    [5,1] [7,3]       Every pair of 2 will be compared atleast once therefore maximum comparison here
                   |     |        |     |
                   |     |        |     |
                 [0,4] [2,6]    [1,5] [3,7]      Maximum Comparison:Every pair of set is used in comparison     
                   \     /        \     /                        
                    \   /          \   /
                 [0,2,4,6]      [1,3,5,7]        Maximum comparison again: Every pair of set compared
                      \             /
                       \           / 
                     [0,1,2,3,4,5,6,7]          
```




## * Maximum Sum Subarray

給一個陣列，找出連續的區間和要最大。

最笨的方法就是窮舉起點和終點，加上要計算起點到終點的和所以複雜度是 {{< math_inline >}}O(n^{3}){{< /math_inline >}}。

用分治法可以達到 {{< math_inline >}}O(n lg(n)){{< /math_inline >}}。

我們首先可以觀察到答案的左右兩側一定是負數（因為如果是正的那就可以再大一點）。

![](./Screenshot%20from%202023-09-21%2000-30-53.png)

每一次的答案一定要麼在
1. 左邊子序列
2. 右邊子序列
3. 橫跨左右（可以從中間往左右兩邊去找）

不過這個問題可以用貪心的演算法達到 {{< math_inline >}}O(n){{< /math_inline >}} 的複雜度！

具體來說，每次累積從起點到目前的總和，如果總和是負的就放棄（總和、起點重置），如果累積總和比目前為止最大就更新最大。




## FFT

請移駕[快速傅立葉變換](/blog/posts/2023-09-25-fft/)


# Segment Tree（線段樹）

課本沒教，之後單獨寫一篇

Array representation


## 矩陣乘法

基本：
![](./Screenshot%20from%202023-09-21%2000-41-07.png)

D&C：
```
T(n) = 8 T(n/2) + Theta(n^2)
也是 Theta(n^3)，沒有比較快
```

Strassen D&C：
```
T(n) = 7 T(n/2) + Theta(n^2)
變成 Theta(n^lg(7)) = Theta(n^2.81)
```

目前最快解法（2.376）：https://people.csail.mit.edu/virgi/matrixmult-f.pdf


## Fibonacci
可以參考 https://zh.wikipedia.org/zh-tw/%E6%96%90%E6%B3%A2%E9%82%A3%E5%A5%91%E6%95%B0

最慢的解法（一般式）：
![](./Screenshot%20from%202023-09-21%2000-22-30.png)
```
Omega(phi^n)
```

Bottom-up：
```
Theta(n)
```

矩陣（線性代數）：
![](./Screenshot%20from%202023-09-21%2000-27-08.png)
然後用前面提到的 divide and conquer 算次方。
```
Theta(lg(n))
```

## H Tree
![](./Screenshot%20from%202023-09-21%2000-51-47.png)
![](./Screenshot%20from%202023-09-21%2000-54-51.png)

H Tree 是一種碎形。
可以參考：https://en.wikipedia.org/wiki/H_tree





## Binary Search
從排序好的陣列找元素。

## Power（次方）
分成奇偶 case。







# 參考
* [時間複雜度 —遞迴（上）](https://mycollegenotebook.medium.com/%E6%99%82%E9%96%93%E8%A4%87%E9%9B%9C%E5%BA%A6-%E9%81%9E%E8%BF%B4-%E4%B8%8A-f6d51a462394)
* [時間複雜度 —遞迴（下）](https://mycollegenotebook.medium.com/%E6%99%82%E9%96%93%E8%A4%87%E9%9B%9C%E5%BA%A6-%E9%81%9E%E8%BF%B4-%E4%B8%8B-master-th-307ad4608ab6)