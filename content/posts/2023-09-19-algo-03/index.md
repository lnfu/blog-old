---
title: "[課程筆記] 演算法概論（三） - 排序"
date: 2023-10-17T10:43:24+08:00
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

這裡介紹所有常見的排序。

# Bubble Sort

- Stable
- 平均：比較次數 {{< math_inline >}}O(n^{2}){{< /math_inline >}}
- 最好：就是全部都排好序了，完全不用交換，比較次數 {{< math_inline >}}O(n){{< /math_inline >}}
- 最壞：比較次數 {{< math_inline >}}O(n^{2}){{< /math_inline >}}

# Insertion Sort

- Stable
- 平均：比較次數 {{< math_inline >}}O(n^{2}){{< /math_inline >}}
- 最好：就是全部都排好序了，完全不用交換，比較次數 {{< math_inline >}}O(n){{< /math_inline >}}
- 最壞：比較次數 {{< math_inline >}}O(n^{2}){{< /math_inline >}}

# Heapsort

- Unstable
- 平均、最好、最壞都是 {{< math_inline >}}O(n lg(n)){{< /math_inline >}}

## Heap 演算法（TODO）

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

## Heapsort 演算法
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


# Quicksort

- Unstable
- 平均：{{< math_inline >}}O(n lg(n)){{< /math_inline >}}
- 最好：{{< math_inline >}}O(n lg(n)){{< /math_inline >}}
- 最壞：每次選到的 pivot 很爛（選到最小或最大），{{< math_inline >}}O(n^{2}){{< /math_inline >}}

選擇 pivot 的方法（TODO）

機率證明複雜度（TODO）

# Couting Sort

- Stable
- 平均、最好、最壞都是 {{< math_inline >}}O(n + k){{< /math_inline >}}

```
n 個 element
k 個 key
element = (key, value)
```

計算每個 key 的次數，然後根據 key 放到對應的位置。

範例（假設 key 就等於 value）：
```python
A = [5, 4, 4, 3, 2, 4, 2, 1] # n = 8

# k = 0  1  2  3  4  5
C1 = [0, 1, 2, 1, 3, 1]
C2 = [0, 1, 3, 4, 7, 8] # <-

B = [-1, -1, -1, -1, -1, -1, -1, -1]
B = [ 1, -1, -1, -1, -1, -1, -1, -1] # C = [0, 0, 3, 4, 7, 8]
B = [ 1, -1,  2, -1, -1, -1, -1, -1] # C = [0, 0, 2, 4, 7, 8]
B = [ 1, -1,  2, -1, -1, -1,  4, -1] # C = [0, 0, 2, 4, 6, 8]
B = [ 1,  2,  2, -1, -1, -1,  4, -1] # C = [0, 0, 1, 4, 6, 8]
B = [ 1,  2,  2,  3, -1, -1,  4, -1] # C = [0, 0, 1, 3, 6, 8]
B = [ 1,  2,  2,  3, -1,  4,  4, -1] # C = [0, 0, 1, 3, 5, 8]
B = [ 1,  2,  2,  3,  4,  4,  4, -1] # C = [0, 0, 1, 3, 4, 8]
B = [ 1,  2,  2,  3,  4,  4,  4,  5] # C = [0, 0, 1, 3, 4, 7]
```

# Radix Sort

和 Counting Sort 概念上類似。

```
RADIX-SORT(A, n, d)
1   for i = 1 to d
2       use a stable sort to sort array A[1:n] on digit i
```

以十進位為例就是先比個位數、再比十位數、百位數...



# Conclusion

![](./Screenshot%20from%202023-10-17%2010-17-06.png)

