---
title: "2023 10 19 Unity Game Tips"
date: 2023-10-19T23:00:38+08:00
draft: false
author: "Enfu Liao"
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

這篇會說明一些在用 Unity 開發遊戲時常常會需要的功能以及實做方式。

# 小地圖
分成三個部份：
1. 新增 Camera：用 Orthographic、Size 記得調整
2. 新增 Render Texture：Size 和 UI 的地圖要設成一樣大
3. 新增 UI - RawImage

把 Render Texture 拉到 Camera 的 target texture。

把 Render Texture 拉到 RawImage 的 texture。

# 移動

Input 可以用 GetKey 或是 GetAxis（新版，建議使用）。

```
Input.GetAxis("Horizontal");
Input.GetAxis("Vertical");
```

有幾種情況和方法：
1. 角色什麼也不是，直接改變他的 transform.position
2. 角色是 rigidbody，可以用 `AddForce()` 給予移動的力，不過這樣會滑行，所以可以設定阻力（drag）
3. 角色是 character controller 可以用 `Move()` 或是 `SimpleMove()`

> 首先，它们的共同点都是用来移动的，参数都是Verctor3类型的向量。不同的是，Move这个方法它的实际作用和Transform组件中的translate几乎一样。而且它计算速度是以帧计算的。(cc表示角色控制器)所以需要乘每帧时间间隔：cc.Move(Vector3.forward * Time.deltaTime * 5).而SimpleMove它就比较特别了。当你使用它来移动你的目标时，它就具备了“重力”，而且移动的时候，它时以秒为单位的。不用乘时间：cc.SimpleMove(Vector3.forward * 5). 

# 視角

```
Input.GetAxis("Mouse X");
Input.GetAxis("Mouse Y");
```

先乘上 frameTime 和 sens。

- 相機上下（和左右 why？）
- 角色左右

member variable 紀錄 rotation，再去加上（減去）滑鼠的數值。


# Gound Check

跳躍會用到

- 用 trigger/collision 做
- 用 raycast 做（我比較 prefer）