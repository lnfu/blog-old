---
title: "快速傅立葉變換"
date: 2023-09-25T23:52:07+08:00
draft: false
author: "Enfu Liao"
math: true
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

競程一有講過快速傅立葉變換（fast Fourier transform；FFT），只是那時候放棄理解><

這次修演算法又提到所以終於打算把她看懂了。

## FFT 在解決什麼問題？
假設我們有兩個多項式
{{< math_block >}}
f(x) = \sum_{i=0}^{n-1} a_{i} x^{i} \\
g(x) = \sum_{i=0}^{n-1} b_{i} x^{i}
{{< /math_block >}}
要如何求出
{{< math_block >}}
(f \cdot g)(x) = \sum_{i=0}^{2n-2} (a_{i} b_{i}) x^{i} \\
{{< /math_block >}}

> 之所以兩個多項式都可以寫成冪次是 n，是因為就算不同也可以把冪次比較小的多項式看成是更高冪次的係數都是 0。

很顯然，我們可以用 `O(n^2)` 的時間解決這個問題（兩邊係數各自乘起來），不過是否還存在更快的解法呢？

整個 FFT 的步驟可以拆成三個部份：
1. 將多項式 {{< math_inline >}}f(x){{< /math_inline >}} 和 {{< math_inline >}}g(x){{< /math_inline >}} 從係數表示轉換成點值表示
2. 算出 {{< math_inline >}}h(x) = f(x) \cdot g(x){{< /math_inline >}}
3. 將 {{< math_inline >}}h(x){{< /math_inline >}} 從點值表示換回係數表示

其中，步驟一的複雜度是 `O(n lg(n))`，步驟二是 `O(n)`、步驟三是 `O(n lg(n))`，所以整個 FFT 的複雜度是 `O(n lg(n))`。

### 步驟一：將係數表示轉換成點值表示
> n 個點可以唯一的決定一個 n - 1 次多項式

> 步驟二會說明點值表示的好處

這邊會需要引入幾個單位根（root of unity）的重要性質。

n 次單位根指的是所有 n 次冪為 1 的複數，其在複數平面的上會構成一正 n 多邊形。（高中數學）

{{< math_block >}}
\omega_{n}^{k} = \cos \frac{2 \pi k}{n} + i \sin \frac{2 \pi k}{n}, k = 0, 1, 2, ... n - 1
{{< /math_block >}}

> {{< math_inline >}}\omega_{n}^{0} = \omega_{n}^{n} = 1{{< /math_inline >}}（繞一圈）

#### 性質一：{{< math_inline >}}\omega_{rn}^{rk} = \omega_{n}^{k}{{< /math_inline >}}

{{< math_block >}}
\begin{align}
\omega_{rn}^{rk} & = \cos \frac{2 \pi rk}{rn} + i \sin \frac{2 \pi rk}{rn} \nonumber \\
&  = \cos \frac{2 \pi k}{n} + i \sin \frac{2 \pi n}{n} \nonumber \\
&  = \omega_{n}^{k} \nonumber
\end{align}
{{< /math_block >}}


#### 性質二：{{< math_inline >}}\omega_{n}^{k + \frac{n}{2}} = -\omega_{n}^{k}{{< /math_inline >}}

{{< math_block >}}
\begin{align}
\omega_{n}^{k + \frac{n}{2}} & = \omega_{n}^{k} \left( \cos \frac{\pi n}{n} + i \sin \frac{\pi n}{n} \right) \nonumber \\
& = \omega_{n}^{k} \left( \cos \pi + i \sin \pi \right) \nonumber \\
& = \omega_{n}^{k} \left( -1 + i \cdot 0 \right) \nonumber \\
&  = - \omega_{n}^{k} \nonumber
\end{align}
{{< /math_block >}}


### 步驟二：相乘
有了 2n 個點表示的 {{< math_inline >}}f(x){{< /math_inline >}} 和 {{< math_inline >}}g(x){{< /math_inline >}}，我們只需要把每個點的 Y 座標相乘就是 {{< math_inline >}}h(x) = f(x) \cdot g(x){{< /math_inline >}} 的點值表示了。
> 雖然 n 個點就可以決定出次數是 n - 1 的 {{< math_inline >}}f(x){{< /math_inline >}} 和 {{< math_inline >}}g(x){{< /math_inline >}}，但是因為最後的 {{< math_inline >}}h(x){{< /math_inline >}}次數是 2n - 2，所以我們要 2n 個點。