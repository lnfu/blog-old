---
title: "K8S 簡介"
date: 2023-08-17T18:44:38+08:00
draft: true
author: "Enfu Liao"
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

K8S（Kubernetes）是 Google 開發出來用於調度容器（container）的系統。
> container 和 VM 最大的差別在於沒有 Guest OS（軟體層級的虛擬化）

K8S 的節點（node）分成兩種：control plane 和 worker node。

> 可以理解節點 = 機器（machine）

# Control Plane
負責管理整個集群（cluster）。
> 以前叫做 master

四大元件：
- API Server：負責處理和使用者以及和 worker node 交互的伺服器
    - 使用者可以透過 kubectl / RestfulAPI / Web UI 和 control plane 交互
- Etcd：儲存各種 metadata 的資料庫
    - 例如儲存各個 worker node 狀態
- Scheduler：負責把 Pod 分配到 node 上面（調度）
- Controller Manager：負責監控整個 cluster 狀態並做出反應。

# Worker Node

三個 process：
- Kubelet：和 control plane 的 API server 溝通
- Kube-proxy：負責虛擬網卡（給予 container 網路）
- Container Runtime

## CRI
kubelet 透過 CRI 呼叫 container runtime

常見有：
- Docker（已經不支援）
- containerd
- CRI-O

> CRI 屬於 high level 的 container runtime 規範，OCI 屬於 low level（e.g., runC, Crun）

> Crun 在效率上比 runC 高

## CNI
負責 container 之間、跨節點的網路連接

常見有：
- Flannel


# Control Plane High Avalability (HA)
可以透過 HAProxy + Keepalived 達到 control plane 的 HA

> 目前理解 HAProxy 是 load balancer、Keepalived 會負責檢查機器並且決定主要伺服器。


# Pod 
K8S 基本單位。

一個 pod 可以有多個 container（雖然常常只有一個）。
這些 container 有獨立的 pid, mnt namespace，但是共用 net, uts, ipc namespace。（共享網路...）

# Static Pod
固定在節點上執行。
kubelet 監控、管理。（刪除結束會重啟）






## grafana
```
apiVersion: v1
data:
  grafana.ini: |
    [analytics]
    check_for_updates = true
    [grafana_net]
    url = https://grafana.net
    [log]
    mode = console
    level = debug
    [paths]
    data = /var/lib/grafana/
    logs = /var/log/grafana
    plugins = /var/lib/grafana/plugins
    provisioning = /etc/grafana/provisioning
    [server]
    domain = 'grafana.efliao.test.cc.cs.nctu.edu.tw'
    root_url = 'http://grafana.efliao.test.cc.cs.nctu.edu.tw'
    # http_port = 30678
    [auth.generic_oauth]
    email_attribute_name = uid
    tls_skip_verify_insecure = true
    enabled = true
    client_id = 285
    client_secret = ykweRHC0o4pEshJyOOGCPWC8JK3AnjGSIRXrczBY
    scopes = openid email
    auth_url = "http://oauth.alpha.test.cc.cs.nctu.edu.tw/oauth/authorize?"
    token_url = "http://oauth.alpha.test.cc.cs.nctu.edu.tw/oauth/token"
    api_url = "http://oauth.alpha.test.cc.cs.nctu.edu.tw/api/user"
kind: ConfigMap
```