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

只需要在 worker 上面安裝就好。

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

然後要編輯 `/etc/crio/crio.conf`（第 98 行開始）：
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

# Control Plane HA（使用 HAProxy、keepalived）

根據 CC 的 Wiki，我們可以透過產生 haproxy 和 keepalived 的 static pod 來達到 control plane HA。

我會將 HA 入口架在 10.2.2.137:8443，並導向三個 control-planes 10.2.2.[131-133]:6443。

> 6443 是 Kubernetes API server 的 port

首先，先在所有 control plane（10.2.2.[131-133]）新增 `/etc/haproxy/haproxy.cfg`。
```
mkdir -p /etc/haproxy/
vim /etc/haproxy/haproxy.cfg
```

寫入以下內容：
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

然後寫兩個 static pod 的配置檔案（yaml），我都先在第一個 control plane node 上面做（efliao-k8s-c1）。

haproxy：
```
# /etc/kubernetes/manifests/haproxy.yaml
kind: Pod
apiVersion: v1
metadata:
  annotations:
    scheduler.alpha.kubernetes.io/critical-pod: ""
  name: kube-haproxy
  namespace: kube-system
spec:
  hostNetwork: true
  priorityClassName: system-cluster-critical
  containers:
  - name: kube-haproxy
    image: docker.io/haproxy:1.7-alpine
    resources:
      requests:
        cpu: 100m
    volumeMounts:
    - name: haproxy-cfg
      readOnly: true
      mountPath: /usr/local/etc/haproxy
  volumes:
  - name: haproxy-cfg
    hostPath:
      path: /etc/haproxy
```

keepalived：
```
# /etc/kubernetes/manifests/keepalived.yaml
kind: Pod
apiVersion: v1
metadata:
  annotations:
    scheduler.alpha.kubernetes.io/critical-pod: ""
  name: kube-keepalived
  namespace: kube-system
spec:
  hostNetwork: true
  priorityClassName: system-cluster-critical
  containers:
  - name: kube-keepalived
    image: docker.io/osixia/keepalived:2.0.17
    env:
    - name: KEEPALIVED_VIRTUAL_IPS
      value: 10.2.2.137
    - name: KEEPALIVED_INTERFACE
      value: ens192
    - name: KEEPALIVED_UNICAST_PEERS
      value: "#PYTHON2BASH:['10.2.2.131', '10.2.2.132', '10.2.2.133']"
    resources:
      requests:
        cpu: 100m
    securityContext:
      privileged: true
      capabilities:
        add:
        - NET_ADMIN
```



# 開啟需要的 port（防火牆設定）

## control plane

```
firewall-cmd --add-port={6443,2379,2380,8443,10248,10250,10255,10257,10259}/tcp --permanent
firewall-cmd --reload # 加完記得要 reload
```

> 8443 是要給 control plane HA 使用的。

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
firewall-cmd --reload # 加完記得要 reload
```

| Protocol | Direction | Port Range  | Purpose            | Used By             |
|----------|-----------|-------------|--------------------|---------------------|
| TCP      | Inbound   | 10250       | Kubelet API        | Self, Control plane |
| TCP      | Inbound   | 30000-32767 | NodePort Services† | All                 |




# 建立集群

規格上面寫說 CNI 選擇 Flannel，模式為 host-gw。

https://wiki.cs.nctu.edu.tw/index.php/Flannel

Flannel 支援至少兩種模式： VXLAN 和 host-gw 。前者將各節點以 VXLAN 的方式連結成一個虛擬 LAN ，因此各節點可以在不同的網段中；後者透過修改路由的方式促成各節點之間的網路連通，因此其先備條件是所有機器必須在同一個 LAN 當中。

在 efliao-k8s-c1 執行：
```
kubeadm init --control-plane-endpoint=10.2.2.137:8443 --pod-network-cidr=10.244.0.0/16 --upload-certs
```

> 10.244.0.0/16 是 Flannel 預設分配 IP 的範圍。

輸出結果：

```
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of the control-plane node running the following command on each as root:

  kubeadm join 10.2.2.137:8443 --token aksrrq.5lx1h32ffved6ezp \
	--discovery-token-ca-cert-hash sha256:62daa58135e22888376988582ebc0bc43c911e0b7ccf5f60f14c2c98ec8f4df5 \
	--control-plane --certificate-key d6efaae506d1d4fc4629aed84d15e8b6906f12eaaa98ca243b6afd8b9ca3550b

Please note that the certificate-key gives access to cluster sensitive data, keep it secret!
As a safeguard, uploaded-certs will be deleted in two hours; If necessary, you can use
"kubeadm init phase upload-certs --upload-certs" to reload certs afterward.

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 10.2.2.137:8443 --token aksrrq.5lx1h32ffved6ezp \
	--discovery-token-ca-cert-hash sha256:62daa58135e22888376988582ebc0bc43c911e0b7ccf5f60f14c2c98ec8f4df5
```

然後根據提示訊息在其他節點執行 `kubeadm join` 指令來加入集群。

集群建立後，config 檔案會被放在 /etc/kubernetes/admin.conf，你需要這個檔案來操作 kubectl 。











等所有節點都加入集群後，先運行 Flannel：
```
wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml -O- \
  | sed 's/vxlan/host-gw/' \
  | kubectl create -f-
```


---















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





---
