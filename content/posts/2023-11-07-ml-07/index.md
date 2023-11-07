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


# ANN

假設是 2D feature space（只有 2 個 feature）。

```
    | f1 | f2 | target
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

{{< math_block >}}
\sum_{i=0}^{n} w_{i}x_{i} \ge 0
x_{0} = 1
{{< /math_block >}}
