---
title: "CC 新訓 - K8S"
date: 2023-10-26T11:24:41+08:00
draft: false
author: "Enfu Liao"
tags: ["CC"]

# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

這東西我拖了超九，還是趕快把她弄凍搞定好了。

# 更新
目前機器都裝好 CRIO 了。

先更新系統好了，順便拔掉兩層 sudo 鬼東西。

```sh
yum update -y
yum remove -y cs-pam-sudo
```

# 開啟 worker 的硬體虛擬化

這是為了之後跑 kata-container。

https://wiki.cs.nctu.edu.tw/index.php/Kubernetes_%E5%B9%B3%E5%8F%B0%E5%BB%BA%E7%BD%AE_-_Kata_Container

先把 worker 三台關機。

然後將 `Action` > `Edit settings` > `Virtual Hardware` > `CPU` > `Hardware virtualization` > `Expose hardware assisted virtualization to the guest OS` 打勾。

然後開機並檢查是否有 vmx/smv 功能（有輸出內容就可以了）。

```
grep -E '(vmx|svm)' /proc/cpuinfo
```

# 安裝 kata container

想一想乾脆也先裝一裝 kata container，之後就不用在用了。

```
dnf install -y centos-release-advanced-virtualization
dnf module disable -y virt:rhel
source /etc/os-release
cat <<EOF | sudo -E tee /etc/yum.repos.d/kata-containers.repo
[kata-containers]
name=Kata Containers
baseurl=http://mirror.centos.org/\$contentdir/\$releasever/virt/\$basearch/kata-containers
enabled=1
gpgcheck=1
skip_if_unavailable=1
EOF
dnf install -y kata-containers
```

```
dnf install -y qemu-kvm
```

```
kata-runtime kata-check     # 確認是否有正確安裝
```

然後要編輯 `/etc/crio/crio.conf`：
```
[crio.runtime]
manage_ns_lifecycle = true

[crio.runtime.runtimes.kata-runtime]
runtime_path = "/usr/bin/containerd-shim-kata-v2"
runtime_type = "vm"
runtime_root = "/run/vc"
```

重新啟動 CRI-O
```
systemctl restart crio
```


# 安裝 kubeadm、kubelet、kubectl（v1.26）
所有機器都要安裝。

```
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kubelet kubeadm kubectl
EOF
```

```
dnf install -y kubeadm-1.26.0 kubelet-1.26.0 kubectl-1.26.0 --disableexcludes=kubernetes
```

然後因為是 CentOS 系統，所以還要開啟這兩個東西：
```
cat << 'EOF' > /usr/lib/systemd/system/kubelet.service.d/20-accounting.conf
[Service]
CPUAccounting=true
MemoryAccounting=true
EOF
```

```
systemctl daemon-reload
systemctl restart kubelet
```

# Control Plane HA

將 HA 入口架在 10.2.2.137:8443，並導向三個 control-planes 10.2.2.[131-133]:6443。


所有 control plane（10.2.2.[131-133]）新增 `/etc/haproxy/haproxy.cfg`
```
mkdir -p /etc/haproxy/
vim /etc/haproxy/haproxy.cfg
```

```
global
  log 127.0.0.1 local0
  log 127.0.0.1 local1 notice
  tune.ssl.default-dh-param 2048

defaults
  log global
  mode http
  option dontlognull
  timeout connect 5000ms
  timeout client 600000ms
  timeout server 600000ms

listen stats
    bind :9090
    mode http
    balance
    stats uri /

frontend kube-apiserver-https
   mode tcp
   bind :8443
   default_backend kube-apiserver-backend

backend kube-apiserver-backend
    mode tcp
    balance roundrobin
    stick-table type ip size 200k expire 30m
    stick on src
    server apiserver1 10.2.2.90:6443 check
    server apiserver2 10.2.2.91:6443 check
    server apiserver3 10.2.2.92:6443 check
```













---

# k8s & crio version

集群已經建立好了，不過先確認一些東西。

```sh
kubectl version --short                                     # k8s 版本
tr \\0 ' ' < /proc/"$(pgrep kubelet)"/cmdline | grep crio   # 確認是用 crio（不太確定這樣對不對）
cat /var/run/crio/version                                   # crio 版本
```

```plaintext
Client Version: v1.26.0
Kustomize Version: v4.5.7
Server Version: v1.26.9
```

```plaintext
/usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --container-runtime-endpoint=unix:///var/run/crio/crio.sock --pod-infra-container-image=registry.k8s.io/pause:3.9
```

```plaintext
"1.26.4+unknown"
```

看起來沒什麼問題，繼續。


# 開啟相關 port

```
# 先重設防火牆
rm -f /etc/firewalld/zones/public.xml
rm -f /etc/firewalld/zones/public.xml.old
firewall-cmd --complete-reload
```

## control plane

```
firewall-cmd --add-port={6443,2379,2380,10248,10250,10255,10257,10259}/tcp --permanent
```

| Protocol | Direction | Port Range | Purpose                 | Used By              |
|----------|-----------|------------|-------------------------|----------------------|
| TCP      | Inbound   | 6443       | Kubernetes API server   | All                  |
| TCP      | Inbound   | 2379-2380  | etcd server client API  | kube-apiserver, etcd |
| TCP      | Inbound   | 10250      | Kubelet API             | Self, Control plane  |
| TCP      | Inbound   | 10259      | kube-scheduler          | Self                 |
| TCP      | Inbound   | 10257      | kube-controller-manager | Self                 |

## worker

```
firewall-cmd --add-port={80,443,10248,10250,10255,30000-32767}/tcp --permanent
```

| Protocol | Direction | Port Range  | Purpose            | Used By             |
|----------|-----------|-------------|--------------------|---------------------|
| TCP      | Inbound   | 10250       | Kubelet API        | Self, Control plane |
| TCP      | Inbound   | 30000-32767 | NodePort Services† | All                 |


## reload firewall 設定

```
firewall-cmd --reload
```


---


```
global
  log 127.0.0.1 local0
  log 127.0.0.1 local1 notice
  tune.ssl.default-dh-param 2048

defaults
  log global
  mode http
  option dontlognull
  timeout connect 5000ms
  timeout client 600000ms
  timeout server 600000ms

listen stats
    bind :9090
    mode http
    balance
    stats uri /

frontend kube-apiserver-https
   mode tcp
   bind :8443
   default_backend kube-apiserver-backend

backend kube-apiserver-backend
    mode tcp
    balance roundrobin
    stick-table type ip size 200k expire 30m
    stick on src
    server apiserver1 10.2.2.131:6443 check
    server apiserver2 10.2.2.132:6443 check
    server apiserver3 10.2.2.133:6443 check

```