## Pod

### Pod概述

> Pod 是 k8s 系统中可以创建和管理的最小单元，是资源对象模型中由用户创建或部署的最 小资源对象模型，也是在 k8s 上运行容器化应用的资源对象，其他的资源对象都是用来支 撑或者扩展 Pod 对象功能的，比如控制器对象是用来管控 Pod 对象的，Service 或者 Ingress 资源对象是用来暴露 Pod 引用对象的，PersistentVolume 资源对象是用来为 Pod 提供存储等等，k8s 不会直接处理容器，而是 Pod，Pod 是由一个或多个 container 组成 Pod 是 Kubernetes 的最重要概念，每一个 Pod 都有一个特殊的被称为”根容器“的 Pause 容器。Pause 容器对应的镜 像属于 Kubernetes 平台的一部分，除了 Pause 容器，每个 Pod 还包含一个或多个紧密相关的用户业务容器

![](../../images/k8s5.png)

![](../../images/k8s6.png)

- Pod vs 应用

  每个 Pod 都是应用的一个实例，有专用的 IP

- Pod vs 容器

  一个 Pod 可以有多个容器，彼此间共享网络和存储资源，每个 Pod 中有一个 Pause 容器保 存所有的容器状态， 通过管理 pause 容器，达到管理 pod 中所有容器的效果

- Pod vs 节点

  同一个 Pod 中的容器总会被调度到相同 Node 节点，不同节点间 Pod 的通信基于虚拟二层网 络技术实现

- Pod vs Pod

  普通的 Pod 和静态 Pod

### Pod 特性

- 资源共享

  一个 Pod 里的多个容器可以共享存储和网络，可以看作一个逻辑的主机。共享的如 namespace,cgroups 或者其他的隔离资源。

  多个容器共享同一 network namespace，由此在一个 Pod 里的多个容器共享 Pod 的 IP 和 端口 namespace，所以一个 Pod 内的多个容器之间可以通过 localhost 来进行通信,所需要 注意的是不同容器要注意不要有端口冲突即可。不同的 Pod 有不同的 IP,不同 Pod 内的多 个容器之间通信，不可以使用 IPC（如果没有特殊指定的话）通信，通常情况下使用 Pod 的 IP 进行通信。

  一个 Pod 里的多个容器可以共享存储卷，这个存储卷会被定义为 Pod 的一部分，并且可 以挂载到该 Pod 里的所有容器的文件系统上。

- 生命周期短暂

  Pod 属于生命周期比较短暂的组件，比如，当 Pod 所在节点发生故障，那么该节点上的 Pod 会被调度到其他节点，但需要注意的是，被重新调度的 Pod 是一个全新的 Pod,跟之前的 Pod 没有半毛钱关系。

- 平坦的网络

  K8s 集群中的所有 Pod 都在同一个共享网络地址空间中，也就是说每个 Pod 都可以通过其 他 Pod 的 IP 地址来实



### Pod定义

- 下面是 yaml 文件定义的 Pod 的完整内容

  ```yaml
  apiVersion: v1
  kind: Pod
    metadata: //元数据
  name: string
  namespace: string
  labels:
    -name: string
  annotations:
    -name: string
  spec:
  
  ```

  

  Pod 的基本使用方

  在 kubernetes 中对运行容器的要求为：容器的主程序需要一直在前台运行，而不是后台运行。应用需要改造成前台运行的方式。如果我们创建的 Docker 镜像的启动命令是后台执行程序，则在 kubelet 创建包含这个容器的 pod 之后运行完该命令，即认为 Pod 已经结束， 将立刻销毁该 Pod。如果为该 Pod 定义了 RC，则创建、销毁会陷入一个无 限循环的过程中。 Pod 可以由 1 个或多个容器组合而成。

- 

  

  

  

  

  

  

  