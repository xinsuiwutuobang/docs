## 单master集群

### 平台规划图

![sk8s1](E:\yangfei\技术总结\images\sk8s1.png)

### 安装要求

- 一台或多台机器，操作系统 CentOS7.x-86_x64
- 硬件配置：2GB 或更多 RAM，2 个 CPU 或更多 CPU，硬盘 30GB 或更多 
- 集群中所有机器之间网络互通
- 可以访问外网，需要拉取镜像 
- 禁止 swap 分区

### 最终目标

1. 在所有节点上安装 Docker 和 kubeadm 
2. 部署 Kubernetes Master
3. 部署容器网络插件
4. 部署 Kubernetes Node，将节点加入 Kubernetes 集群中 
5. 部署 Dashboard Web 页面，可视化查看 Kubernetes 资源

### 准备环境

![](E:\yangfei\技术总结\images\sk8s4.png)

| 角色       | IP          |
| ---------- | ----------- |
| k8s-master | 10.10.30.42 |
| k8s-node1  | 10.10.30.44 |
| k8s-node2  | 10.10.30.94 |

### 系统初始化

- 关闭防火墙

  ```shell
  systemctl stop firewalld
  systemctl disable firewalld 
  ```

- 关闭selinux

  ```shell
  sed -i 's/enforcing/disabled/' /etc/selinux/config # 永久
  setenforce 0 # 临时
  ```

- 关闭swap

  ```shell
  swapoff -a # 临时
  vim /etc/fstab # 永
  ```

- 主机名

  ```shell
  hostnamectl set-hostname <hostname>
  ```

- master添加host

  ```shell
  cat >> /etc/hosts << EOF
  10.10.30.42 k8s-master
  10.10.30.44 k8s-node1
  10.10.30.94 k8s-node2
  EOF
  ```

- 将桥接IPv4流量传递到iptables的链

  ```shell
  cat > /etc/sysctl.d/k8s.conf << EOF
  net.bridge.bridge-nf-call-ip6tables = 1
  net.bridge.bridge-nf-call-iptables = 1
  EOF
  sysctl --system # 生效
  ```

- 时间同步

  ```shell
  yum install ntpdate -y
  ntpdate time.windows.com
  ```


### 所有系统安装docker、kubeadm、kubelet

>  Kubernetes 默认 CRI（容器运行时）为 Docker，因此先安装 Docker。

- 安装Docker

  ```shell
  wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo -O /etc/yum.repos.d/docker-ce.repo
  yum -y install docker-ce-18.06.1.ce-3.el7
  ln -s /home/mydata/docker /var/lib/docker
  systemctl enable docker && systemctl start docker
  docker --version
  ```

- 添加阿里云 YUM 软件源，所有节点

  - 设置仓库地址

    ```shell
    cat > /etc/docker/daemon.json << EOF
    {
    "registry-mirrors": ["https://b9pmyelo.mirror.aliyuncs.com"]
    }
    EOF
    ```

  - 重启docker

    ```shell
    systemctl restart docker
    ```

    

  - 添加 yum 源

    ```shell
    cat > /etc/yum.repos.d/kubernetes.repo << EOF
    [kubernetes]
    name=Kubernetes
    baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
    enabled=1s
    gpgcheck=0
    repo_gpgcheck=0
    gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
    https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
    EOF
    ```

  - 安装 kubeadm，kubelet 和 kubectl

    ```shell
    yum install -y kubelet-1.18.0 kubeadm-1.18.0 kubectl-1.18.0
    systemctl enable kubelet
    #卸载命令
    yum remove  kubelet kubeadm kubectl
    ```

    

### 部署 Kubernetes Master

- 在 10.10.30.42（Master）执行

  ```shell
  kubeadm init \
  --apiserver-advertise-address=10.10.30.42 \
  --image-repository registry.aliyuncs.com/google_containers \
  --kubernetes-version v1.18.0 \
  --service-cidr=10.96.0.0/12 \
  --pod-network-cidr=10.244.0.0/16
  ```

  初始化后输出内容

  ```shell
  Your Kubernetes control-plane has initialized successfully!
  
  To start using your cluster, you need to run the following as a regular user:
  
    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
  
  You should now deploy a pod network to the cluster.
  Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
    https://kubernetes.io/docs/concepts/cluster-administration/addons/
  
  Then you can join any number of worker nodes by running the following on each as root:
  
  kubeadm join 10.10.30.42:6443 --token zhovqb.2u53nl6peeumqs05 \
      --discovery-token-ca-cert-hash sha256:e52721e2f7f555dd0587cedef640849a2aa349fcaad43e4759a924374a4a0fda 
  ```

  

  由于默认拉取镜像地址 k8s.gcr.io 国内无法访问，这里指定阿里云镜像仓库地址.

- 使用kubectl工具

  ```shell
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
  kubectl get nodes
  ```

### 加入 Kubernetes Node 

- 在 10.10.30.44/94（Node）执行

  向集群添加新节点，执行在kubeadm init输出的kubeadm join命令：

  ```shell
  kubeadm join 10.10.30.42:6443 --token esce21.q6hetwm8si29qxwn \
  --discovery-token-ca-cert-hash
  sha256:00603a05805807501d7181c3d60b478788408cfe6cedefedb1f97569708be9c5
  ```

  默认token有效期为24小时，当过期之后，该token就不可用了。这时就需要重新创建token，操作如下：

  ```shell
  kubeadm token create --print-join-command
  ```

### master安装 Pod 网络插件（CNI）NotReady-> running状态

```shell
wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

默认镜像地址无法访问，sed命令修改为docker hub镜像仓库。

```shell
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

kubectl get pods -n kube-system
NAME                          READY   STATUS    RESTARTS   AGE
kube-flannel-ds-amd64-2pc95   1/1     Running   0          72s
```



### 测试 kubernetes 集群

在 Kubernetes 集群中创建一个 pod，验证是否正常运行

```shell
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --port=80 --type=NodePort
kubectl get pod,svc
```

```shell
[root@kmaster:/home/mydata]# kubectl get pod,svc
NAME                        READY   STATUS    RESTARTS   AGE
pod/nginx-f89759699-lnm8h   1/1     Running   0          115s

NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
service/kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP        64m
service/nginx        NodePort    10.98.197.21   <none>        80:30206/TCP   10s
```

访问地址：http://NodeIP:Port

- 10.10.30.44:30206
- 10.10.30.94:30206

### 注意点

#### 版本号需要一致

- docker docker-ce-18.06.1.ce-3.el7
- kubelet-1.18.0 kubeadm-1.18.0 kubectl-1.18.0
- --kubernetes-version v1.18.0 \