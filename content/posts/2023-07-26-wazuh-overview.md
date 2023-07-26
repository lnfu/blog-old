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

> 2023-07-12 CC System 會議有提到看能不能和現有的 OpenSearch 集群做整合。

## Wazuh vs. OSSIM

> 之後研究一下 OSSIM 是啥？