## 资源编排YAML

### YAML文件概述

k8s 集群中对资源管理和资源对象编排部署都可以通过声明样式（YAML）文件来解决，也 就是可以把需要对资源对象操作编辑到 YAML 格式文件中，我们把这种文件叫做资源清单文 件，通过 kubectl 命令直接使用资源清单文件就可以实现对大量的资源对象进行编排部署 了

### YAML文件书写格式

#### YAML介绍

YAML ：仍是一种标记语言。为了强调这种语言以数据做为中心，而不是以标记语言为重点。 

YAML 是一个可读性高，用来表达数据序列的格式。

#### YAML 基本语法

- 使用空格做为缩进 
- 每行开头缩进两个空格
- 支付后缩进一个空格
- 使用 --- 表示新的yaml文件
- 低版本缩进时不允许使用 Tab 键，只允许使用空格 
- 使用#标识注释，从这个字符一直到行尾，都会被解释器忽略

#### 如何快速编写yaml文件

- 使用kubectl create命令生成yaml文件,只输出不执行yaml，之后可以修改并执行

  ```shell
  kubectl create deployment web --image=nginx -o yaml --dry-run > mynginx.yaml
  ```

  ```shell
  [root@kmaster:/home/mydata/k8s]# kubectl create deployment web --image=nginx -o yaml --dry-run > mynginx.yaml
  W0628 15:08:19.994772  599992 helpers.go:636] --dry-run is deprecated and can be replaced with --dry-run=client.
  [root@kmaster:/home/mydata/k8s]# ll
  总用量 4
  -rw-r--r-- 1 root root 376 6月  28 15:08 mynginx.yaml
  [root@kmaster:/home/mydata/k8s]# cat mynginx.yaml 
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    creationTimestamp: null
    labels:
      app: web
    name: web
  spec:
    replicas: 1
    selector:
      matchLabels:
        app: web
    strategy: {}
    template:
      metadata:
        creationTimestamp: null
        labels:
          app: web
      spec:
        containers:
        - image: nginx
          name: nginx
          resources: {}
  status: {}
  ```

- 使用kubectl get 命令导出yaml文件,使用部署好的资源来导出yaml

  ```shell
  kubectl get deploy
  kubectl get deploy nginx -o=yaml > my2yaml
  ```

  ```yaml
  [root@kmaster:/home/mydata/k8s]# kubectl get deploy
  NAME    READY   UP-TO-DATE   AVAILABLE   AGE
  nginx   1/1     1            1           24h
  [root@kmaster:/home/mydata/k8s]# kubectl get deploy nginx -o=yaml  > my2yaml
  [root@kmaster:/home/mydata/k8s]# cat my2yaml 
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    annotations:
      deployment.kubernetes.io/revision: "1"
    creationTimestamp: "2022-06-27T06:52:29Z"
    generation: 1
    labels:
      app: nginx
    name: nginx
    namespace: default
    resourceVersion: "9464"
    selfLink: /apis/apps/v1/namespaces/default/deployments/nginx
    uid: 5dff1529-67ee-4a4a-ba6f-bacec4a3a0b6
  spec:
    progressDeadlineSeconds: 600
    replicas: 1
    revisionHistoryLimit: 10
    selector:
      matchLabels:
        app: nginx
    strategy:
      rollingUpdate:
        maxSurge: 25%
        maxUnavailable: 25%
      type: RollingUpdate
    template:
      metadata:
        creationTimestamp: null
        labels:
          app: nginx
      spec:
        containers:
        - image: nginx
          imagePullPolicy: Always
          name: nginx
          resources: {}
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
        dnsPolicy: ClusterFirst
        restartPolicy: Always
        schedulerName: default-scheduler
        securityContext: {}
        terminationGracePeriodSeconds: 30
  status:
    availableReplicas: 1
    conditions:
    - lastTransitionTime: "2022-06-27T06:53:06Z"
      lastUpdateTime: "2022-06-27T06:53:06Z"
      message: Deployment has minimum availability.
      reason: MinimumReplicasAvailable
      status: "True"
      type: Available
    - lastTransitionTime: "2022-06-27T06:52:29Z"
      lastUpdateTime: "2022-06-27T06:53:06Z"
      message: ReplicaSet "nginx-f89759699" has successfully progressed.
      reason: NewReplicaSetAvailable
      status: "True"
      type: Progressing
    observedGeneration: 1
    readyReplicas: 1
    replicas: 1
    updatedReplicas: 1
  ```

  

#### YAML 支持的数据结

- 对象

  键值对的集合，又称为映射(mapping) / 哈希（hashes） / 字典（dictionary）

  ```yaml
  #对象类型：对象的一组键值对，使用冒号结构表示
  name: tom
  age: 18
  
  #yaml 也运行另一种写法，将所有键值对写成一个行内对象
  hash: {name: tom, age: 18}
  ```
  
- 数组

  一组按次序排列的值，又称为序列（sequence） / 列表 （list）

  ```yaml
  #数组类型：一组连词线开头的行，构成一个数组
  people:
    - tom
    -jack
  #数组也可以采用行内表示法
  people: [tom,jack]
  ```

- 纯量（scalars）：

  单个的、不可再分的值

  ```yaml
  #纯量是最基本的，不可再分的值。
  #数值直接以字面量的形式表示
  number: 12.3
  isSet: true
  #null用~表示
  parent: ~
  #时间采用ISO8601格式
  iso8601: 2001-12-11t11:23:11.11-05:23
  #日期采用ISO8601格式的年月日表示
  date: 1971-07-31
  #yaml允许使用两个感叹号，强制转换数据类型
  e: !!str 123
  e: !!str true
  #字符串默认不使用引号，如果字符串中包含空格或特殊字符，需要放在单引号之中，双引号也可以但是不会对特殊字符转义
  str: '内容\n字符串'
  str1: "内容\n字符串"
  #单引号之中如果还有单引号，必须连续使用两个单引号转义
  str2: 'hello ''word'
  #字符串可以写成多行，从第二行开始，必须有一个单空格缩进。换行符会被转换为空格。多行字符串可以使用|保留换行符，也可以使用 > 折叠换行。
  ```

#### 资源清单描述方法

- 在 k8s 中，一般使用 YAML 格式的文件来创建符合我们预期期望的 pod,这样的 YAML 文件称为资源清单。

- 常用字段

  > 必须存在的属性

  | 参数名                 | 字段类型 | 说明                                                         |
  | ---------------------- | -------- | ------------------------------------------------------------ |
  | version                | String   | k8s api版本，目前基本是v1，可以使用kubectl api-version命令查询 |
  | kind                   | String   | yaml文件定义的资源类型和角色，如：pod                        |
  | metadata               | Object   | 原数据对象，固定值写metadata                                 |
  | metadata.name          | String   | 元数据对象名称，比如命名pod的名称                            |
  | metadata.namespace     | String   | 元数据对象的命名空间，由我们自己填写                         |
  | spec                   | Object   | 详细定义对象，固定值写spec                                   |
  | spec.container[]       | list     | spec对象的容器列表                                           |
  | spec.container[].name  | String   | 容器的名字                                                   |
  | spec.container[].image | String   | 用到的镜像的名称                                             |

  > spec 主要对象

  | 参数名                                      | 字段类型 | 说明                                                         |
  | ------------------------------------------- | -------- | ------------------------------------------------------------ |
  | spec.containers[].name                      | String   | 定义容器的名字                                               |
  | spec.containers[].image                     | String   | 定义要用到的容器的名字                                       |
  | spec.containers[].imagePullPolicy           | String   | 定义镜像拉取策略，有Always：每次尝试重新拉取镜像，Never：仅适用本地镜像，IfNotPresent：如果本地镜像有就用本地镜像，没有就拉取在线镜像 三个值可选 |
  | spec.containers[].command[]                 | List     | 指定容器启动命令，因为是数组，所以可以指定多个，不指定则适用镜像打包时适用的启动命令。 |
  | spec.containers[].args[]                    | List     | 指定容器启动时命令参数，因为是数组，所以可以指定多个         |
  | spec.containers[].workingDir                | String   | 指定容器的工作目录                                           |
  | spec.containers[].volumeMounts[]            | List     | 指定容器内部的存储券配置                                     |
  | spec.containers[].volumeMounts[].name       | String   | 指定可以被容器挂载的存储券的名称                             |
  | spec.containers[].volumeMounts[].mountPath  | String   | 指定可以被容器挂载的容器券的路径                             |
  | spec.containers[].volumeMounts[].readOnly   | String   | 设置存储券路径的读写模式，true或false，默认为读写模式        |
  | spec.containers[].ports[]                   | List     | 指定容器需要用到的端口列表                                   |
  | spec.containers[].ports[].name              | String   | 指定端口名称                                                 |
  | spec.containers[].port[].containerPort      | String   | 指定容器需要监听的端口号                                     |
  | spec.containers[].port[].hostPort           | String   | 指定容器所在主机需要监听的端口号，默认上面containerPort相同，注意设置了hostPort同一台主机无法启动该容器的相同副本，因为主机的端口号不能相同，这样会冲突 |
  | spec.containers[].port[].protocol           | String   | 指定端口协议，支持tcp和udp，默认为tcp                        |
  | spec.containers[].env[]                     | List     | 指定容器运行需设置的环境变量列表                             |
  | spec.containers[].env[].name                | String   | 指定环境变量名称                                             |
  | spec.containers[].env[].value               | String   | 指定环境变量值                                               |
  | spec.containers[].resources                 | Object   | 指定资源限制和资源请求的值（设置容器的资源上限）             |
  | spec.containers[].resources.limits          | Object   | 指定设置容器运行时资源的运行上限                             |
  | spec.containers[].resources.limits.cpu      | String   | 指定cpu的限制，单位为core数，将用于docker run --cpu-shares参数 |
  | spec.containers[].resources.limits.memory   | String   | 指定memory内存限制，单位为MIB，GIB                           |
  | spec.containers[].resources.requests        | Object   | 指定容器启动和调度的限制设置                                 |
  | spec.containers[].resources.requests.cpu    | String   | CPU请求，单位为core数，容器启动时初始化可用数量              |
  | spec.containers[].resources.requests.memory | String   | 内存请求，单位为MIB，GIB容器启动时初始化可用数量             |

  >  额外的参数

  | 参数名                | 字段类型 | 说明                                                         |
  | --------------------- | -------- | ------------------------------------------------------------ |
  | spec.restartPolicy    | String   | 定义pod重启策略，可以选择值为Always、Onfailure、Never，默认值为Always。Always:pod一旦终止运行，则无论容器时如何终止的，kubelet服务将会重启该容器。OnFailure：只有pod已非零退出码终止时，kubelet才会重启该容器。如果容器正常结束退出码为0，则kubelet将不会重启该容器。Never：pod终止后，kubelet将退出码报告为Master，不会重启该pod |
  | spec.nodeSelector     | Object   | 定义node的label过滤标签，以key:value格式指定                 |
  | spec.imagePullSecrets | Object   | 定义pull镜像使用secret名称，以name:secretkey格式指定         |
  | spec.hostNetwork      | Boolean  | 定义是否使用主机网络模式，默认值为falsse。设置true则表示使用宿主机网络，不适用docker网桥，同时设置了true将无法在同一台宿主机上启动第二个副本。 |

  > 创建一个namespace

  ```yaml
  apiVersion: v1
  kind: Namespace
  metadata:
  	name: test
  ```

  > 创建一个pod

  ```yaml
  paiVersion: v1
  kind: pod
  metadata:
  	name: pod1
  spec:
  	containers:
  	- name: nginx-containers
  	  image: nginx:latest
  
  ```

  

  

  

  

  

  

  

