---
title: "[課程筆記] 演算法概論"
date: 2023-09-12T09:38:21+08:00
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

## RAM
Random Access Machine
1. 記憶體存取都是花費一單位的時間
2. 指令依序一個一個的被讀取

## T(n)
Running Time（步驟的總數量）

n = input size

## Complexity of Recurrence Relation
可以參考：
* https://mycollegenotebook.medium.com/%E6%99%82%E9%96%93%E8%A4%87%E9%9B%9C%E5%BA%A6-%E9%81%9E%E8%BF%B4-%E4%B8%8A-f6d51a462394
* https://mycollegenotebook.medium.com/%E6%99%82%E9%96%93%E8%A4%87%E9%9B%9C%E5%BA%A6-%E9%81%9E%E8%BF%B4-%E4%B8%8B-master-th-307ad4608ab6

![](https://slideplayer.com/7351407/24/images/slide_1.jpg)

以 merge sort 舉例，每個 sorting problem 可以拆成兩個 sorting problem，其花費的時間是原本的一半，所以 a = 2、b = 2，另外拆開（divide）的時間只需要 O(1)，合併（combine）則需要迴圈跑過一次所以是 O(n)。

要解出 recurrence 複雜度的一般式有兩個方法：
1. backward substitution
2. Master Theorem

### substitution method
用數學歸納法（strong induction）證明。

> 可以先用 recursion-tree 去分析


-> 2023.08.25

### Master Theorem
![](https://cdn-images-1.medium.com/max/1200/0*SMhJVzBPbBuiGOws.png)


## Divide and Conquer
### Merge Sort
1. 分成一半來 sort（recursive）
2. 用 T(n) = n 跑過一次比較誰先誰後

```
T(n) = 2 T(n/2) + O(n)
```

### Binary Search
Find an element in a **sorted** array.

```
T(n) = T(n/2) + Theta(1)
```

### Power（次方）
分成奇偶 case。

```
T(n) = T(n/2) + Theta(1)
T(n) = Theta(lg(n))
```

### Fibonacci
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


### * Maximum Subarray
> 首先可以觀察到答案的左右兩側一定是負數

![](./Screenshot%20from%202023-09-21%2000-30-53.png)

答案一定要麼在
1. 左邊子序列
2. 右邊子序列
3. 橫跨左右（可以從中間往左右兩邊去找）

```
FIND-MAX-CROSSING-SUBARRAY(A, low, mid, high)

    // Find a maximum subarray of the form A[i ... mid].
    leftsum = -
    sum = 0
    for i = mid downto low
        sum = sum + A[i]
        if sum > leftsum
            leftsum = sum
            maxleft = i

    // Find a maximum subarray of the form A[mid + 1.. j].
    rightsum = -
    sum = 0
    for j = mid + 1 to high
        sum = sum + A[j]
        if sum > rightsum
            rightsum = sum
            maxright = j

    // Return the indices and the sum of the two subarrays.
    return (maxleft, maxright, leftsum + rightsum)


FIND-MAXIMUM-SUBARRAY(A, low, high)
    if high == low
        return (low, high, A[low]) // base case: only one element
    else mid = (low +high)/2
    
    (leftlow, lefthigh, leftsum) = FIND-MAXIMUM-SUBARRAY(A, low, mid)
    (rightlow, righthigh, rightsum) = FIND-MAXIMUM-SUBARRAY(A, mid + 1, high)
    (crosslow, crosshigh, crosssum) = FIND-MAX-CROSSING-SUBARRAY(A, low, mid, high)

    if leftsum >= rightsum and leftsum >= crosssum
        return (leftlow, lefthigh, leftsum)
    else if rightsum >= leftsum and rightsum >= crosssum
        return (rightlow, righthigh, rightsum)
    else 
        return (crosslow, crosshigh, crosssum)


Initial call: FIND-MAXIMUM-SUBARRAY(A, 1, n)
```
![](./Screenshot%20from%202023-09-21%2000-40-17.png)


### 矩陣乘法

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



### H Tree
![](./Screenshot%20from%202023-09-21%2000-51-47.png)
![](./Screenshot%20from%202023-09-21%2000-54-51.png)

H Tree 是一種碎形。
可以參考：https://en.wikipedia.org/wiki/H_tree



### FFT
請移駕[快速傅立葉變換](/blog/posts/2023-09-25-fft/)

-> 2023.09.21



## Heap：Heapsort & Priority Queue

Heapify 函數：
```
MAX-HEAPIFY(A, i)
1   l = LEFT(i) // i 左邊的 index
2   r = RIGHT(i)
3   largest = i // 從 i, l, r 中選出最大的
4   if l <= A.heap-size and A[l] > A[i]
5       largest = l
6   if r <= A.heap-size and A[r] > A[largest]
7       largest = r
8   if largest != i
9       exchange A[i] with A[largest]
10      MAX-HEAPIFY(A, largest) // 繼續往下
```

複雜度：
```
T(n) <= T(2n/3) + Theta(1) // 最慘情況下，葉子和 internal node 數量相同，往下的那一棵 subtree 占總共的 2/3
O(lg n)
```

建立 max heap：
```
BUILD-MAX-HEAP(A, n)
1   A.heap-size = n
2   for i = ceil(n/2) downto 1
3       MAX-HEAPIFY(A, i)
```

直覺上，這樣的複雜度是 O(n lg n)，不過因為越靠近葉子 Heapify 所要花的時間越小，其更緊的複雜度是 O(n)。
> 每一層分開算加總
> h = height = 0, 1, 2, ..., k
> k = lg(n)
![](./Screenshot%20from%202023-09-26%2010-53-41.png)

Heapsort 演算法：
> 重複把最大的（root）往最後丟，最後就會變成一個由小到大排序好的陣列
```
HEAPSORT(A)
    BUILD-MAX-HEAP(A) // O(n)
    for i = A.length down to 2
        exchange A[1] with A[i]
        A.size = A.size - 1
        MAX-HEAPIFY(A, 1) // O(lg n)
```

複雜度：O(n lg(n))

Priority Queue：
1. Insert(S, x) -> 插入時維護 heap 結構
2. Maximum(S)
3. Extract-Max(S)



## Segment Tree（線段樹）

課本沒教，之後單獨寫一篇

Array representation




## QuickSort

Worse Case：選到的 pivot 很爛（e.g., 最大）

機率證明 average case time complexity

# Decision-Tree



# Couting Sort & Radix Sort
