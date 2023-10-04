---
title: "[課程筆記] 遊戲程式設計（三）"
date: 2023-10-04T14:37:52+08:00
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

這次講的是 unity 的物理（physics）元件。

注意，應避免使用大量物理元件導致運算量太大。

# 三大物理元件（component）
1. Collider：box, sphere, capsule, mesh, terrain 五種
2. Rigidbody：質量、受到重力影響
3. Physic Material：材質（摩擦係數、角摩擦係數、彈性...）

一般的 GameObject 預設都會加入對應的 collider。


# 碰撞條件
1. 兩個物體都有 collider
2. 至少其中一邊有 non-kinematic rigidbody

non-kinematic rigidbody 會受到外力（`AddForce()`, `AddTorque()`）、重力、碰撞影響

例如：棒球是 non-kinematic、球棒是 kinematic。

所以總共有三種 collider：
1. static
2. rigidbody
3. kinematic rigidbody：和 static 類似（可以收到碰撞事件）
4. trigger：直接穿過去，但是會有觸發事件（`OnTriggerEnter()`、`OnTriggerStay()`、`OnTriggerExit()`）。

## 三種碰撞事件
```c#
void OnCollisionEnter(Collision collision)
void OnCollisionStay(Collision collision)
void OnCollisionExit(Collision collision)
```


## Layer 設定
設定不同層是否發生碰撞。

**Edit** -> **ProjectSetting** -> **Physics**

