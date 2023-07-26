---
title: "2023 07 26 Wazuh Overview"
date: 2023-07-26T14:19:49+08:00
draft: false
author: "Enfu Liao"
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

最近我在 CC 分配到的工作是 survey EDR solution。

在文章開始之前，我先講一下一些相關的專有名詞，假設我們維護多台主機。

* HIDS（Host-Based Intrusion Detection System）：安裝在需要監控的主機上，當主機偵測到異常事件時，回傳資訊給管理者（預警）。
* EDR（Endpoint Detection and Response）：除了回傳資訊以外更加積極，例如直接封鎖惡意軟體、用戶（active response）
* NTA（Network Traffic Analysis）：可以想成是在網路層面的 HIDS。
* NDR（Network Detection and Response）：可以想成是在網路層面的 EDR。
* XDR：同時監控檢測主機和網路，甚至進行比對。
* SIEM：集中各種資安產品所收集到的資訊，透過預先設定好的規則來進行分析、預警甚至自動化腳本回應（active response）

學長說希望找到的產品符合以下條件：
1. open source
2. 能夠偵測異常網路流量
3. 能夠偵測重要檔案被改動
4. 能夠主動封鎖用戶

## OSSEC

一開始我找到 OSSEC 這套軟體。

OSSEC 支援多種系統

並且有以下功能：
1. file integrity 檢查
2. log 檢查
3. rootcheck（惡意軟體檢查）

除此之外還可以設定 active response 自動回應異常事件。

## Wazuh

不過後來我看到了 Wazuh 這個專案。

Wazuh 可以看成是 OSSEC + OpenSearch + OpenSCAP 的組合。

OSSEC 前面已經講過了，省略。

OpenSearch 主要負責 Wazuh 的搜尋和 Web UI 部份（Kibana）

OpenSCAP 的 SCAP 是指 Security Content Automation Protocol，主要用來衡量系統有沒有漏洞。

> Wazuh server = OSSEC + OpenSCAP
> Wazuh indexer + Wazuh dashboard = OpenSearch + Kibana + ...

> 2023-07-12 CC System Meeting：Wazuh 能不能和現有的 OpenSearch 集群做整合。

> 選擇 Wazuh 最大的好處是社群好像是目前看起來最活躍的，所以有什麼問題比較容易解決。

### 安裝 Wazuh
我目前使用 source code 安裝 Wazuh manager（Wazuh indexer 和 Wazuh dashboard 先不安裝）。

安裝方式參考官方網站的[說明文檔](https://documentation.wazuh.com/current/deployment-options/wazuh-from-sources/index.html)。

**server 部份：**
```
yum install make cmake gcc gcc-c++ python3 python3-policycoreutils automake autoconf libtool openssl-devel yum-utils
yum-config-manager --enable powertools
yum install libstdc++-static -y
yum install epel-release yum-utils -y
yum-builddep python34 -y
```

```
curl -Ls https://github.com/wazuh/wazuh/archive/v4.4.5.tar.gz | tar zx
cd wazuh-4.4.5
./install.sh
```

接著依照提示完成安裝即可（選擇 manager），我所有選項都選預設值。

```
systemctl start wazuh-manager
```

**agent 部份：**
```
yum install make gcc gcc-c++ python3 python3-policycoreutils automake autoconf libtool openssl-devel cmake
yum-config-manager --enable powertools
yum install libstdc++-static -y
```

接下來和 server 一樣把安裝檔案下載下來執行 `install.sh` 腳本（選擇 agent）依照提示安裝。


不管是 server 或是 agent，設定檔（預設）會放在 `/var/ossec/etc/` 下。
> 為什麼 server 和 agent 要分成兩個設定檔案？


## OSSIM
> 之後研究一下 OSSIM 是啥？