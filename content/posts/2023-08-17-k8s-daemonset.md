---
title: "K8S DaemonSet"
date: 2023-08-17T14:52:39+08:00
draft: false
author: "Enfu Liao"
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---


`whoami-daemonset.yaml`

```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: whoami-daemonset
  labels:
    app: whoami
spec:
  selector:
    matchLabels:
      app: whoami
  template:
    metadata:
      labels:
        app: whoami
    spec:
      containers:
        - name: whoami
          image: containous/whoami
          ports:
            - containerPort: 80
```


`whoami-service.yaml`


```
apiVersion: v1
kind: Service
metadata:
  name: whoami-service
spec:
  type: NodePort
  selector:
    app: whoami
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30080  # You can specify the desired NodePort here
```

```
kubectl apply -f whoami-daemonset.yaml
kubectl apply -f whoami-service.yaml
```

```
kubectl get daemonset
kubectl get service
```