---
layout: post
title: Mac 安装 minikube
categories:  minikube
description: minikube install
keywords: ninikube, mac
---

为了方便大家开发和体验 Kubernetes，社区提供了可以在本地部署的 Minikube。 Minikube 是 K8S 官方为了开发者能在个人电脑上运行 K8S 而提供的一套工具。是通过 Go 语言编写，通过虚拟化管理程序，创建出一个运行在虚拟机内的单节点集群。

**目录**

{:toc}


# 前提

安装 Oracle VM VisualBox

# 安装过程

先安装 minikube, 使用下面命令（由于墙的问题，所以指定国内的地址）


```
curl -Lo minikube http://kubernetes.oss-cn-hangzhou.aliyuncs.com/minikube/releases/v1.2.0/minikube-darwin-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
```


![minikube 安装](/images/posts/minikube_install.png)

    
# 启动 `minikube` 


 ```
 minikube start --registry-mirror=https://registry.docker-cn.com
 ```

 ![minikube_start](/images/posts/minikube_start.png)

