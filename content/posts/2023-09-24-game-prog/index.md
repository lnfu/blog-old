---
title: "[課程筆記] 遊戲程式設計"
date: 2023-09-24T22:19:02+08:00
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

一樣是隔壁朱宏國老師開的課。

主要是教 Unity。

## Unity 基本操作

- `Q`：View
- `W`：移動物體
- `E`：旋轉物體
- `R`：縮放物體


### Inspector 窗格：顯示 GameObject 帶有的 Component

> GameOjbect 可能是 Model、光源、Camera、粒子效果...

component 是 GameObject 的組成元素，用來控制 GameObject 的行為，可能是：
1. Transform：位置、旋轉、縮放
2. Mesh Filter
3. Mesh Renderer
4. Sphere Collider
5. Material：材質（貼圖、反光...）

### Unity 遊戲設計架構
![Unity 遊戲設計架構](./Screenshot%20from%202023-09-24%2022-34-13.png)

### 光源設定
> 光源越多運算成本越高

- Type：種類（平行光、點光源、spotlight、區域光...）
- Range：可照射的最遠距離
- Angle：聚光燈展開角度
- Color
- Mode：運算模式
- Intensity：強度（亮度）
- Inderect Multiplier：環境光影響係數
- Shadow Type：產生的影子類型
- Flare：光源位置的圖像材質

### Camera
可以用 **Align With View** 選項來套用成目前視角


## Asset Store
現在版本都只能在瀏覽器去新增


## C# Script

推薦使用 Visual Studio Code


### 重要事項
- 當使用 `public` 宣告變數時（並且將 Script 套用到 GameObject），可以直接在 Inspector 窗格修改該變數。但是在 play mode 修改都不會保存。
- C# 不做自動轉型！請這樣寫：`float a = (float)0;`
- `float` 要有 f 結尾

### 算數
- `System.Math`：double
- `UnityEngine.Mathf`：float（由於 Unity 參數都是 float，所以建議使用這個就好）

```c#
using UnityEngine;

public class Test : MonoBehavior
{
    void Start()
    {
        float a = Mathf.Sin(Mathf.PI);
        float b = Mathf.Abs(a - 10.0f);
    }
}
```


### 隨機
```c#
using UnityEngine;

public class Test : MonoBehavior
{
    void Start()
    {
        int seed = 40;
        Random.InitState(seed);
        float rng_1 = Random.value; // 0 ~ 1 之間（包含 0, 1）
        float rng_2 = Random.Range(-5f, 5f); // -5 ~ 5 之間（包含 -5, 5）
    }
}
```


### 向量
- Vector2
- Vector3
- Vector4（較少使用）

```
Vector3 vec = new Vector3(1.0f, 2.0f, 3.0f);
Debug.Log(vec.magnitude);
Debug.Log(vec.normalized);
Debug.Log(vec.x);
Debug.Log(vec.y);
Debug.Log(vec.z);
```

![](./Screenshot%20from%202023-09-24%2023-34-45.png)


### Unity C# 程式架構

所有的 script 都是繼承自 MonoBehavior 類別。他有四個 method：
1. `Awake()`：
1. `Start()`：
1. `Update()`：
1. `FixedUpdate()`：