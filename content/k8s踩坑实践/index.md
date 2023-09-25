+++
title = "Kubernetes 实践记录"
date = 2023-05-06 14:47:19
slug = "202305061447"

[taxonomies]
tags = ["Kubernetes"]
categories =  ["Container"]

+++

<!-- more -->

## 相关概念

### service和pod的关系

在 Kubernetes 中，Service 和 Pod 之间是一种逻辑上的关系，用于解决 Kubernetes 集群中服务的发现和负载均衡问题。

Pod 是 Kubernetes 中最小的可部署对象，通常包含一个或多个容器。Pod 可以单独部署，也可以由 Deployment、StatefulSet 等控制器管理。Pod 可以有自己的 IP 地址和端口号，但这些 IP 地址和端口号是暴露在集群内部的，外部无法直接访问。

Service 则提供了一种将 Pod 暴露给集群外部的方法，通过 Service，可以将 Pod 的 IP 地址和端口号映射到一个 Kubernetes 集群内的虚拟 IP 地址和端口号。当 Service 接收到请求时，它会根据一定的负载均衡算法将请求转发到后端 Pod 中的一个或多个容器中，实现负载均衡和高可用性。

Service 和 Pod 的关系可以看作是一种映射关系，Service 可以将多个 Pod 绑定到同一个 IP 地址和端口号上，并通过负载均衡算法将请求转发到这些 Pod 上，从而实现对 Pod 的访问和管理。

## 常用指令

获取K8s集群下pod的信息

```
kubectl get pod # 同理 kubectl get service
```

运行镜像

```
kubectl run pod名称 --image 镜像名称
```

便于调试/运行完自动删除的常用命令：

```
kubectl run pod名 --restart=Never --rm -i --tty --image 镜像名 --image-pull-policy=Never --port=端口号 -- /bin/sh
```

查看运行状态

```
kubectl describe pod pod名 / service service名
```

将配置应用到pod

```
kubectl apply -f deployment.yaml  #也可以用pod.json
```

删除pod

```
kubectl delete pod pod名
```



查看镜像

```
crictl images
```

