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

```sh
grep -E '(vmx|svm)' /proc/cpuinfo
```

# 安裝 kata container

想一想乾脆也先裝一裝 kata container，之後就不用在用了。

只需要在 worker 上面安裝就好。

```sh
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
```sh
systemctl restart crio
```


# 安裝 kubeadm、kubelet、kubectl（v1.26）
所有機器都要安裝。

```sh
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

```sh
dnf install -y kubeadm-1.26.0 kubelet-1.26.0 kubectl-1.26.0 --disableexcludes=kubernetes
```

然後因為是 CentOS 系統，所以還要開啟這兩個東西：
```sh
cat << 'EOF' > /usr/lib/systemd/system/kubelet.service.d/20-accounting.conf
[Service]
CPUAccounting=true
MemoryAccounting=true
EOF
```

```sh
systemctl daemon-reload
systemctl restart kubelet
```

# Control Plane HA（使用 HAProxy、keepalived）

根據 CC 的 Wiki，我們可以透過產生 haproxy 和 keepalived 的 static pod 來達到 control plane HA。

我會將 HA 入口架在 10.2.2.137:8443，並導向三個 control-planes 10.2.2.[131-133]:6443。

> 6443 是 Kubernetes API server 的 port

首先，先在所有 control plane（10.2.2.[131-133]）新增 `/etc/haproxy/haproxy.cfg`。
```sh
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
```yaml
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
```yaml
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

```sh
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

```sh
firewall-cmd --add-port={80,443,10248,10250,10255,30000-32767}/tcp --permanent
firewall-cmd --reload # 加完記得要 reload
```

| Protocol | Direction | Port Range  | Purpose            | Used By             |
|----------|-----------|-------------|--------------------|---------------------|
| TCP      | Inbound   | 10250       | Kubelet API        | Self, Control plane |
| TCP      | Inbound   | 30000-32767 | NodePort Services† | All                 |




# 建立集群並且執行 Flannel

規格上面寫說 CNI 選擇 Flannel，模式為 host-gw。

https://wiki.cs.nctu.edu.tw/index.php/Flannel

Flannel 支援至少兩種模式： VXLAN 和 host-gw 。前者將各節點以 VXLAN 的方式連結成一個虛擬 LAN ，因此各節點可以在不同的網段中；後者透過修改路由的方式促成各節點之間的網路連通，因此其先備條件是所有機器必須在同一個 LAN 當中。

在 efliao-k8s-c1 執行：
```sh
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

```sh
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```


等所有節點都加入集群後，先運行 Flannel：
```sh
wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml -O- \
  | sed 's/vxlan/host-gw/' \
  | kubectl create -f-
```

# OIDC 設定（選用）

![](./Screenshot%20from%202023-10-26%2019-02-40.png)

修改 `/etc/kubernetes/manifests/kube-apiserver.yaml`，kube-apiserver 會自動載入設定（不過升級集群時會丟失所以要特別處理）。
```sh
sudo vim /etc/kubernetes/manifests/kube-apiserver.yaml
```

```yaml
spec:
  containers:
  - command:
    - --oidc-issuer-url=https://oauth.cs.nctu.edu.tw
    - --oidc-client-id=987
    - --oidc-username-claim=uid
    - "--oidc-username-prefix=oidc:"
    - --oidc-groups-claim=groups
    - "--oidc-groups-prefix=oidc-group:"
    - "--oidc-required-claim=is-cs-ta=true"
```

# 部署應用：containous/whoami

- 使用 DaemonSet 佈署網頁服務所需要的 Pod
- 使用 Service type NodePort Expose 出來

`whoami-daemonset.yaml`：
```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: whoami-daemonset
spec:
  selector:
    matchLabels:
      app: whoami               # 要和 spec.template.labels 一樣
  template:
    metadata:
      labels:
        app: whoami
    spec:
      containers:
      - name: whoami-container
        image: containous/whoami
      # tolerations:
      # - key: node-role.kubernetes.io/control-plane
      #   operator: Exists
      #   effect: NoSchedule
      # - key: node-role.kubernetes.io/master
      #   operator: Exists
      #   effect: NoSchedule
```


`whoami-svc`：
```yaml
apiVersion: v1
kind: Service
metadata:
  name: whoami-service
spec:
  type: NodePort
  selector:
    app: whoami
  ports:
  - name: http
    targetPort: 80
    port: 80
    nodePort: 30080
```

# 部署 LoadBalancer：OpenELB

- 用最簡單的 VIP Mode 來實作

> 另一個有名的 LB 是 MetalLB。

> 使用 LoadBalancer 的好處：网络流量在云服务端就会被分流，就能够避免 NodePort 方式的单点故障和性能瓶颈（NodePort 是将集群中的一个主机作为跳板访问后端服务，所有的流量都会经过跳板机，很容易造成性能瓶颈和单点故障，而且经过了一层snat，pod无法看到真正的IP。）[參考](https://wghdr.top/archives/1634)

> OpenELB 主要有 Layer2 Mode 和 BGP Mode

> VIP Mode（還在 beta，has not been fully tested yet and may have unknown issues） 和 Layer2 Mode 相似，只是更簡單。

https://openelb.io/docs/concepts/vip-mode/

https://openelb.io/docs/getting-started/installation/install-openelb-on-kubernetes/

```sh
https://raw.githubusercontent.com/openelb/openelb/master/deploy/openelb.yaml

kubectl apply -f openelb.yaml

kubectl get pod -n openelb-system
```

```
openelb-admission-create-fr7lg    0/1     Completed   0          64s
openelb-admission-patch-flwvq     0/1     Completed   2          64s
openelb-keepalive-vip-42pxp       1/1     Running     0          18s
openelb-keepalive-vip-dfh8d       1/1     Running     0          18s
openelb-keepalive-vip-sspn6       1/1     Running     0          18s
openelb-manager-d6df4dfc4-rbpmn   1/1     Running     0          64s
```

## 建立 Eip object

https://openelb.io/docs/getting-started/configuration/configure-ip-address-pools-using-eip/

```yaml
apiVersion: network.kubesphere.io/v1alpha2
kind: Eip
metadata:
    name: eip-vip-pool
    annotations:
      eip.openelb.kubesphere.io/is-default-eip: "true"
spec:
    address: 10.2.2.138
    protocol: vip
    disable: false
status:
    occupied: false
    poolSize: 1
    firstIP: 10.2.2.138
    lastIP: 10.2.2.138
    ready: true
    v4: true
```

設定成 default 之後就不用在每次建立 service 時打以下內容了：
```yaml
lb.kubesphere.io/v1alpha1: openelb
protocol.openelb.kubesphere.io/v1alpha1: vip
eip.openelb.kubesphere.io/v1alpha2: eip-vip-pool
```

> When creating a Service, generally you need to add the lb.kubesphere.io/v1alpha1: openelb, protocol.openelb.kubesphere.io/v1alpha1: <mode>, and eip.openelb.kubesphere.io/v1alpha2: <Eip name> annotations to the Service to specify that OpenELB is used as the load balancer plugin, either the BGP, Layer 2, or VIP mode is used, and an Eip object is used as the IP address pool. However, if a default Eip object exists, you do not need to add the preceding annotations to the Service and the system automatically assigns an IP address from the default Eip object to the Service. Detailed rules about IP address assignment are as follows:


# 部署 Ingress Controller：Traefik

https://doc.traefik.io/traefik/getting-started/install-traefik/

快被氣死，每次自己寫 yaml 都有問題，有時候還被新舊版本寫法不同搞到，想想還是直接用 helm 安裝好了。

BTW，k8s 和 helm 版本要求：
- Kubernetes 1.16 以上
- Helm version 3.9 以上


## 安裝 helm  

```sh
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

我目前安裝的版本是 helm-v3.13.1-linux-amd64。

## 安裝 Traefik

- Ingress Controller 使用 Traefik v2.7 以上
- 使用 CRD IngressRoute 將 Traefik 自己的 Dashboard Expose 出來
- 使用 LoadBalancer OpenELB 導入流量到 Traefik Ingress Controller
- 外部使用者可以透過 LoadBalancer 的 Port 80 訪問網頁服務(proto http)
- 自簽一張憑證並掛在 Traefik Ingress Controller 上，且讓外部使用者能透過 LoadBalancer 的 Port 443 訪問網頁服務 (proto https)


```sh
helm repo add traefik https://traefik.github.io/charts
helm repo update
helm install traefik traefik/traefik
```

這樣會以 deployment 來安裝 traefik，然後我看安裝完後的 service type 就是 LoadBalancer 所以也不用再改。

安裝完後可以先用 port-forwarding 的方式查看儀表板服務是否可以正常使用。

因為我的 control plane 是在遠端，所以我要先建立 ssh tunnel。

```sh
ssh -L 9000:localhost:9000 <user>@<host>
```

- user: 遠端使用者
- host: 遠端主機

然後在遠端主機做 port-forwarding。
```sh
kubectl port-forward $(kubectl get pods --selector "app.kubernetes.io/name=traefik" --output=name) 9000:9000
```

如此一來，就可以在本機上連到 http://localhost:9000/dashboard/。

注意！dashboard 後面的那個斜線也要打，不然會 404（這什麼爛東西...）。

## 使用 IngressRoute 暴露（expose）儀表板（dashboard）服務

```yaml
# dashboard.yaml
apiVersion: traefik.io/v1alpha1
kind: IngressRoute
metadata:
  name: dashboard
spec:
  entryPoints:
    - web
  routes:
    - match: Host(`efliao.test.cc.cs.nctu.edu.tw`) && (PathPrefix(`/dashboard`) || PathPrefix(`/api`))
      kind: Rule
      services:
        - name: api@internal
          kind: TraefikService
```

## 自簽憑證

https://weirenxue.github.io/2021/06/15/ssl_digital_signed_verify/

先產生私鑰：
```sh
mkdir -p tls
openssl req -x509 -nodes -days 365 -subj "/C=TW/ST=Hsinchu/O=NCTU/OU=CSCC/CN=efliao.test.cc.cs.nctu.edu.tw" -newkey rsa:4096 -keyout ./tls/tls.key -out ./tls/tls.crt
```

`x509` 選項就是自簽憑證（self-signed certificate）


```sh
cd tls
kubectl create secret tls traefik-tls --key tls.key --cert tls.crt
```

然後我們一樣用 IngressRoute 暴露儀表板測試：
```yaml
apiVersion: traefik.io/v1alpha1
kind: IngressRoute
metadata:
  name: dashboard-tls
spec:
  entryPoints:
    - websecure
  routes:
    - match: Host(`efliao.test.cc.cs.nctu.edu.tw`) && (PathPrefix(`/dashboard`) || PathPrefix(`/api`))
      kind: Rule
      services:
        - name: api@internal
          kind: TraefikService
  tls:
    secretName: traefik-tls
```

如果成功就可以連到 https://efliao.test.cc.cs.nctu.edu.tw/dashboard/

> 通常來說，沒有憑證瀏覽器（網址列）會顯示灰色的 Not secure，而像是憑證失效或是我們的自簽憑證就會是紅色的 Not secure。

> 也可以從儀表板的 Routers 標籤頁看到是不是 TLS。

# 部署應用：paulbouwer/hello-kubernetes

- 使用 deployment 的方式部署
- replica = 2
- 需要以 kata containers container runtime 運行
- 使用 Ingress 將服務暴露出來

前面已經裝好 traefik 作為 Ingress Controller，接下來就可以試試看架設 Ingress 服務。

因為要用 kata 作為 container runtime，所以先建立 RuntimeClass。

`kata.yaml`：
```yaml
apiVersion: node.k8s.io/v1
kind: RuntimeClass
metadata:
  name: kata  
handler: kata-runtime
```

然後就是 hello-kubernetes 的 Deployment、Service、Ingress 設定。

`hello-deploy.yaml`：
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-deploy
spec:
  replicas: 2
  selector:
    matchLabels:
      app: hello-kubernetes
  template:
    metadata:
      labels:
        app: hello-kubernetes
    spec:
      runtimeClassName: kata
      containers:
        - name: hello-kubernetes 
          image: paulbouwer/hello-kubernetes:1.10.1
          ports:
            - containerPort: 8080
          env:
            - name: MESSAGE
              value: 一人做事一人當，小叮做事小叮噹

```

`hello-svc.yaml`：
```yaml
apiVersion: v1
kind: Service
metadata:
  name: hello-service
spec:
  selector:
    app: hello-kubernetes
  ports:
    - port: 80
      targetPort: 8080
```

`hello-ingress.yaml`：
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: hello-ingress
spec:
  rules:
    - host: efliao.test.cc.cs.nctu.edu.tw
      http:
        paths:
          - path: "/"
            pathType: Prefix
            backend:
              service:
                name: hello-service
                port:
                  number: 80
```


# 部屬 Monitoring：kube-prometheus-stack

- 使用 Helm Chart 佈署 kube-prometheus-stack
- 使用 dashboard：https://grafana.com/grafana/dashboards/11074-node-exporter-for-prometheus-dashboard-en-v20201010/
- 串接 CC 的 OIDC

```sh
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install kube-prometheus-stack prometheus-community/kube-prometheus-stack 
```