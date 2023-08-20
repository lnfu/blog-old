---
title: "2023 08 17 K8s Overview"
date: 2023-08-17T18:44:38+08:00
draft: false
author: "Enfu Liao"
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---


K8S 的節點（node）分成兩種：control plane 和 worker node。

# Control Plane
負責管理整個集群（cluster）。
> 以前叫做 master

四大元件：
- API Server：負責處理使用者和 K8S 交互的伺服器。
- Etcd：資料庫
- Scheduler：負責把 Pod 分配到 node 上面。
- Controller Manager：負責監控整個 cluster 狀態並做出反應。

# Worker Node
- Kubelet：和 control plane 的 API server 溝通
- Kube-proxy：負責網路（給 container）
- Container Runtime

## CRI
kubelet 透過 CRI 呼叫 container runtime

常見有：
- Docker（已經不支援）
- containerd
- CRI-O

## CNI
負責 container 之間、跨節點的網路連接

常見有：
- Flannel


# Control Plane High Avalability (HA)
可以透過 HAProxy + Keepalived 達到 control plane 的 HA

> 目前理解 HAProxy 是 load balancer、Keepalived 會負責檢查機器並且決定主要伺服器。

# Static Pod
固定在節點上執行。
kubelet 監控、管理。（刪除結束會重啟）

