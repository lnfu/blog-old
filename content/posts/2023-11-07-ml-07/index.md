---
title: "[課程筆記] 機器學習概論（七） - 神經網路"
date: 2023-11-07T14:08:16+08:00
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

一個神經元：

![](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fi.stack.imgur.com%2F7mTvt.jpg&f=1&nofb=1&ipt=03eda6f6878812c2168004c7f1f8fd9a9da541c6334a414e109d6979ef0cb3e5&ipo=images)

1. linear compination of all inputs
2. activation function e.g., step function

![](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fsebastianraschka.com%2Fimages%2Ffaq%2Factivation-functions%2Factivation-functions.png&f=1&nofb=1&ipt=d5549b611038e0bccaabffd5cecee8674c0251bf81c50b8c12eb9df610be836c&ipo=images)


# Perceptron

對單一神經元來說，輸入多個 Binary，輸出一個 Binary。

假設是 2D feature space（只有 2 個 feature）。

```
    | x1 | x2 | target
------------------------
 #1 | ?  | ?  |  +
 #2 | ?  | ?  |  +
 #3 | ?  | ?  |  -
    |    |    |
    |    |    |
    |    |    |    
```

{{< math_block >}}
\sum_{i=1}^{n} w_{i}x_{i} + b \ge 0
{{< /math_block >}}

引入 w0 的表示可以讓原本要分開處理的 bias 更為簡潔。

怎麼理解 b？其實就是 threshold，當超過 threshold 時輸出 1，否則輸出 0。

{{< math_block >}}
\sum_{i=0}^{n} w_{i}x_{i} \ge 0
x_{0} = 1
{{< /math_block >}}

第一層（input layer）的神經元數量會等同於 feature 的數量。




## Sigmoid Function

1. 連續
2. 可微
3. nonlinear



### Error

{{< math_block >}}
E = \frac{1}{2} \sum_{i}^{100} (t_{i} - o_{i})^{2}
{{< /math_block >}}


- {{< math_inline >}}t_{i}{{< /math_inline >}}: true value
- {{< math_inline >}}o_{i}{{< /math_inline >}}: prediction
- {{< math_inline >}}\frac{1}{2}{{< /math_inline >}} 只是為了計算梯度偏微分時可以跟 2 冪次方消掉（計算上方便）


何時會讓 Error 最小？計算 Partial Derrivative。

![](./Screenshot%20from%202023-11-07%2015-54-17.png)

計算微分太困難了。

![](./Screenshot%20from%202023-11-07%2016-15-03.png)

![](./sgd.png)