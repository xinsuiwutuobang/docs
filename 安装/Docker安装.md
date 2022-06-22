## Docker安装

#### 系统要求

- Docker运行在CentOS7.X之上（不支持内核3.8以下的老版本）

#### 通过以下命令查看CentOS内核

```java
uname -r
```

需要保证Docker安装在64位平台

#### 移除非官方软件包

Red Hat 操作系统包含了一个旧版本的 Docker 软件包，该旧版本软件包的名称是 “ docker ”，而新版本是 “ docker-engine ”。因此，如已安装该软件包，那么需要执行以下命令移除。

```shell
$ sudo yum remove docker
```

Tips：执行该命令只会移除旧版本的 Docker , /var/lib/docker 目录中的内容不会被删除，因此，旧版本 Docker 所创建的镜像、容器、卷等都会保留下来。

#### 卸载旧版本

```shell
$ sudo yum remove docker \
                  docker-common \
                  docker-selinux \
                  docker-engine
```

#### 设置 Yum 源

Docker 有多种安装方式，例如 Yum 安装、PRM 包安装、Shell安装等。以下以 Yum 安装方式进行, Docker 分 Docker EE 和 Docker CE 两种版本（EE：企业版，收费的；CE：社区版，不收费）。

1. 安装 yum-utils , 这样就能使用 yum-config-manager 工具设置 Yum 源。

   ```shell
   $ sudo yum install -y yum-utils \
     device-mapper-persistent-data \
     lvm2
   ```

2. 执行以下命令，添加 Docker 的 Yum 源。

   ```shell
   $ sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
   ```

#### 安装Docker

1. 更新 Yum 包的索引

   ```shell
   sudo yum makecache fast
   ```

2. 安装最新版本的 Docker

   ```shell
   sudo yum install docker-ce
   ```

3. 在生产系统中，可能需要安装指定版本的 Docker ，而并不总是安装最新的版本。

   ```shell
   yum list docker-ce --showduplicates | sort -r
   ```

   列出 Docker 版本后，可使用以下命令安装指定版本的 Docker 。

   ```shell
   sudo yum -y install docker-ce-17.09.0.ce
   ```

4. 启动 Docker

   ```shell
   sudo systemctl start docker
   ```

5. 执行以下命令，验证安装是否正确

   ```shell
   sudo docker run hello-world
   ```

6. 查看 Docker 版本

   ```shell
   docker version
   ```

#### 配置镜像加速器

国内访问 Docker Hub 的速度很不稳定，有时甚至出现连接不上的情况。为 Docker 配置镜像加速器，从而解决这个问题。目前国内很多云服务商都提供了镜像加速的服务。常用的镜像加速器有：  
阿里云加速器、DaoCloud加速器等。  
以阿里云加速器为例：  
1、注册阿里云账号，即可在阿里云控制台（[https://cr.console.aliyun.com/cn-zhangjiakou/instances/mirrors](https://cr.console.aliyun.com/cn-zhangjiakou/instances/mirrors)）  
2、按照提示说明，即可配置镜像加速器。

加速器地址：

```java
https://35f7thge.mirror.aliyuncs.com
```

CentOS

1. 安装／升级Docker客户端

   推荐安装1.10.0以上版本的Docker客户端，参考文档[docker-ce](https://yq.aliyun.com/articles/110806)

2. 配置镜像加速器

   针对Docker客户端版本大于 1.10.0 的用户

   您可以通过修改daemon配置文件/etc/docker/daemon.json来使用加速器

   ```shell
   sudo mkdir -p /etc/docker
   sudo tee /etc/docker/daemon.json <<-'EOF'
   {
     "registry-mirrors": ["https://35f7thge.mirror.aliyuncs.com"]
   }
   EOF
   sudo systemctl daemon-reload
   sudo systemctl restart docker
   ```

MacOS

 1. 安装／升级Docker客户端

    对于10.10.3以下的用户 推荐使用Docker Toolbox

    Mac安装文件：http://mirrors.aliyun.com/docker-toolbox/mac/docker-toolbox/

    对于10.10.3以上的用户 推荐使用Docker for Mac

    Mac安装文件：http://mirrors.aliyun.com/docker-toolbox/mac/docker-for-mac/

	2. 配置镜像加速器

    针对安装了Docker Toolbox的用户，您可以参考以下配置步骤：

    创建一台安装有Docker环境的Linux虚拟机，指定机器名称为default，同时配置Docker加速器地址。

    docker-machine create --engine-registry-mirror=https://35f7thge.mirror.aliyuncs.com -d virtualbox default
    查看机器的环境配置，并配置到本地，并通过Docker客户端访问Docker服务。

    ```shell
    docker-machine env default
    eval "$(docker-machine env default)"
    docker info
    ```

    针对安装了Docker for Mac的用户，您可以参考以下配置步骤：

    在任务栏点击 Docker Desktop 应用图标 -> Perferences，在左侧导航菜单选择 Docker Engine，在右侧输入栏编辑 json 文件。将

    https://35f7thge.mirror.aliyuncs.com加到"registry-mirrors"的数组里，点击 Apply & Restart按钮，等待Docker重启并应用配置的镜像加速器。

#### 卸载 Docker

1. 卸载 Docker 软件包

   ```shell
   yum remove docker-ce
   ```

2. 如需删除镜像、容器、卷以及自定义的配置文件，可执行以下命令

   ```shell
   sudo rm -rf /var/lib/docker
   ```

#### 问题

若启动docker的时候报错：`WARNING IPv4 forwarding is disabled. Networking will not work`  
解决方案：

```shell
 vi /etc/sysctl.conf
```

添加配置

```shell
net.ipv4.ip_forward=1
```

重启

```shell
systemctl restart network
```

查看是否成功

```shell
sysctl net.ipv4.ip_forward
```

如果返回为`net.ipv4.ip_forward = 1`则表示成功了