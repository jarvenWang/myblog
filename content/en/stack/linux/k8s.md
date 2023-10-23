---
author: "wangjinbao"
title: "k8s"
date: 2022-11-04 00:00:01
description: "kubernetes，简称K8s，是用8代替名字中间的8个字符“ubernete”而成的缩写。目标是让部署容器化的应用简单并且高效"
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: wangjinbao
authorEmoji: 👻
tags: 
- linux
- categories:

---

## 一、namespace
namespace的作用就是用来隔离资源，将同一集群中的资源划分为相互隔离的组。同一名称空间内的资源名称要唯一，但不同名称空间时没有这个要求。有些k8s资源对象与名称空间没有关系，例如 StorageClass、Node、PersistentVolume 等。

### 方式一：命令行管理
#### 1.1创建
```shell
kubectl create ns test
```

#### 1.2获取
```shell
kubectl get ns
```

#### 1.3删除
该名称空间下所有的资源都将被一起删除
```shell
kubectl delete ns test
```
### 方式二：yaml文件管理
#### 2.1创建
创建一个yaml文件，内容如下：
kind表示要创建的资源类型，此处为Namespace
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```
使用 `apply` 命令创建name为dev的名称空间
```shell
kubectl apply -f dev-ns.yaml
```
#### 2.2获取
查看创建结果
```shell
kubectl get ns
```
参数：`-n`
```shell
kubectl get pod -n kube-system
//结果：
NAME                                     READY   STATUS    RESTARTS      AGE
coredns-5d78c9869d-d6qvr                 1/1     Running   4 (20m ago)   2d16h
coredns-5d78c9869d-nxd7x                 1/1     Running   4 (20m ago)   2d16h
etcd-docker-desktop                      1/1     Running   4 (20m ago)   2d16h
kube-apiserver-docker-desktop            1/1     Running   4 (20m ago)   2d16h
kube-controller-manager-docker-desktop   1/1     Running   4 (20m ago)   2d16h
kube-proxy-qpqsk                         1/1     Running   4 (20m ago)   2d16h
kube-scheduler-docker-desktop            1/1     Running   4 (20m ago)   2d16h
storage-provisioner                      1/1     Running   8 (20m ago)   2d16h
vpnkit-controller                        1/1     Running   4 (20m ago)   2d16h
```
+ NAME：第一列是 pod 的名字，k8s 可以为 pod 随机分配一个五位数的后缀。
+ READY：第二列是 pod 中已经就绪的 docker 容器的数量，pod 封装了一个或多个 docker 容器,1/1的含义为就绪1个容器/共计1个容器。
+ STATUS：第三列是 pod 的当前状态，下面是一些常见的状态：

| 状态名       | 含义 |
|---------    |--    |
| Running     | 运行中 |
| Error       | 异常，无法提供服务 |
| Pending     | 准备中，暂时无法提供服务 |
| Terminaling | 结束中，即将被移除 |
| Unknown     | 未知状态，多发生于节点宕机 |
| PullImageBackOff | 镜像拉取失败 |
 
+ RESTART：k8s 可以自动重启 pod，这一行就是标记了 pod 一共重启了多少次。
+ AGE：pod 一共存在了多长时间。


#### 2.3删除
```shell
kubectl delete -f dev-ns.yaml
```

## 二、kubectl get列出k8s所有资源
`kubectl get` 可以列出 k8s 中所有资源
`kubectl get pod` -- 查看副本控制器pod
`kubectl get svc` -- 查看服务
`kubectl get rs` -- 查看副本控制器
`kubectl get deploy` -- 查看部署
查看更多的信息，就可以指定-o wide参数
如：`kubectl get pod -n kube-system -o wide` 加上这个参数之后就可以看到资源的所在ip和所在节点node了
*** ：记得加上 **<font style color="yellow">-n</font>**
<font style color="yellow">-n</font> 可以说是kubectl get命令使用最频繁的参数了，在正式使用中，我们永远不会把资源发布在默认命名空间。

## 三、kubectl describe 查看详情
kubectl describe命令可以用来查看某一资源的具体信息，他同样可以查看所有资源的详情，不过最常用的还是查看 pod 的详情。
他也同样可以使用 **<font style color="yellow">-n</font>** 参数指定资源所在的命名空间。
如： `kubectl describe pod coredns-5d78c9869d-d6qvr -n kube-system`
```shell
$ kubectl describe pod coredns-5d78c9869d-d6qvr -n kube-system
Name:                 coredns-5d78c9869d-d6qvr
Namespace:            kube-system
Priority:             2000000000
Priority Class Name:  system-cluster-critical
Service Account:      coredns
Node:                 docker-desktop/192.168.65.4
Start Time:           Mon, 16 Oct 2023 17:42:55 +0800
Labels:               k8s-app=kube-dns
                      pod-template-hash=5d78c9869d
Annotations:          <none>
Status:               Running
IP:                   10.1.1.82
IPs:
  IP:           10.1.1.82
Controlled By:  ReplicaSet/coredns-5d78c9869d
Containers:
  coredns:
    Container ID:  docker://c3778f5414c9fae0e988ad5e8826de296d9be51712f9131e339150e8e9594de2
    Image:         registry.k8s.io/coredns/coredns:v1.10.1
    Image ID:      docker://sha256:97e04611ad43405a2e5863ae17c6f1bc9181bdefdaa78627c432ef754a4eb108
    Ports:         53/UDP, 53/TCP, 9153/TCP
    Host Ports:    0/UDP, 0/TCP, 0/TCP
    Args:
      -conf
      /etc/coredns/Corefile
    State:          Running
      Started:      Thu, 19 Oct 2023 09:51:06 +0800
    Last State:     Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Wed, 18 Oct 2023 10:17:27 +0800
      Finished:     Thu, 19 Oct 2023 09:50:50 +0800
    Ready:          True
    Restart Count:  4
    Limits:
      memory:  170Mi
    Requests:
      cpu:        100m
      memory:     70Mi
    Liveness:     http-get http://:8080/health delay=60s timeout=5s period=10s #success=1 #failure=5
    Readiness:    http-get http://:8181/ready delay=0s timeout=1s period=10s #success=1 #failure=3
    Environment:  <none>
    Mounts:
      /etc/coredns from config-volume (ro)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-tqbb6 (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      coredns
    Optional:  false
  kube-api-access-tqbb6:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              kubernetes.io/os=linux
Tolerations:                 CriticalAddonsOnly op=Exists
                             node-role.kubernetes.io/control-plane:NoSchedule
                             node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason          Age                From     Message
  ----     ------          ----               ----     -------
  Normal   SandboxChanged  53m                kubelet  Pod sandbox changed, it will be killed and re-created.
  Normal   Pulled          53m                kubelet  Container image "registry.k8s.io/coredns/coredns:v1.10.1" already present on machine
  Normal   Created         53m                kubelet  Created container coredns
  Normal   Started         53m                kubelet  Started container coredns
  Warning  Unhealthy       53m (x4 over 53m)  kubelet  Readiness probe failed: HTTP probe failed with statuscode: 503
```
+ 实例名称：Name: coredns-5d78c9869d-d6qvr
+ 所处命名空间：Namespace: kube-system
+ 所在节点：Node: docker-desktop/192.168.65.4
+ 启动时间：Start Time: Mon, 16 Oct 2023 17:42:55 +0800
+ 标签：Labels: k8s-app=kube-dns pod-template-hash=5d78c9869d
+ 注解：Annotations: <none>
+ 当前状态：Status: Running
+ 所在节点 IP：IP: 10.1.1.82
+ 由那种资源生成/控制：Controlled By: ReplicaSet/coredns-5d78c9869d
其中几个比较常用的，例如 **`Node`**、**`labels`** 和 **`Controlled By`**。
a. 通过 `Node` 你可以快速定位到 pod 所处的机器，从而检查该机器是否出现问题或宕机等。
b. 通过 `labels` 你可以检索到该 pod 的大致用途及定位。
c. 通过 `Controlled By` ，你可以知道该 pod 是由那种 k8s 资源创建的，然后就可以使用`kubectl get <资源名>`来继续查找问题。
例如上文 `ReplicaSet/coredns-5d78c9869d`，就可以通过 `kubectl get ReplicaSet -n kube-system` 来获取上一节资源的信息。

### Events 故障原因
`kubectl describe <资源名> <实例名>` 可以查看一个资源的详细信息
最常用的还是比如 `kubectl describe pod <pod名> -n <命名空间>` 来获取一个 pod 的基本信息。如果出现问题的话，可以在获取到的信息的末尾看到 `Event` 段落，其中记录着导致 pod **故障的原因**。

## 三、kubectl logs 查看日志

如果你想查看一个 pod 的具体日志，就可以通过 `kubectl logs <pod名>` 来查看。
注意，这个只能查看 `pod` 的日志。通过添加 <font color="yellow">-f</font> 参数可以 `持续` 查看日志。
例如：查看 `kube-system` 命名空间中某个 `coredns pod` 的日志，注意修改 pod 名称：
`kubectl logs -f -n kube-system coredns-5d78c9869d-d6qvr`
```shell
$ kubectl logs -f -n kube-system coredns-5d78c9869d-d6qvr
[INFO] plugin/kubernetes: waiting for Kubernetes API before starting server
[INFO] plugin/kubernetes: waiting for Kubernetes API before starting server
[INFO] plugin/ready: Still waiting on: "kubernetes"
[INFO] plugin/ready: Still waiting on: "kubernetes"
[INFO] plugin/ready: Still waiting on: "kubernetes"
[INFO] plugin/kubernetes: waiting for Kubernetes API before starting server
[INFO] plugin/kubernetes: waiting for Kubernetes API before starting server
[INFO] plugin/ready: Still waiting on: "kubernetes"
[INFO] plugin/kubernetes: waiting for Kubernetes API before starting server
[INFO] plugin/kubernetes: waiting for Kubernetes API before starting server
[INFO] plugin/kubernetes: waiting for Kubernetes API before starting server
[INFO] plugin/kubernetes: waiting for Kubernetes API before starting server
[INFO] plugin/kubernetes: waiting for Kubernetes API before starting server
[WARNING] plugin/kubernetes: starting server with unsynced Kubernetes API
.:53
[INFO] plugin/reload: Running configuration SHA512 = 591cf328cccc12bc490481273e738df59329c62c0b729d94e8b61db9961c2fa5f046dd37f1cf888b953814040d180f52594972691cd6ff41be96639138a43908
CoreDNS-1.10.1
linux/arm64, go1.20, 055b2c3
[WARNING] plugin/kubernetes: Kubernetes API connection failure: Get "https://10.96.0.1:443/version": net/http: TLS handshake timeout
```