## kubectl

1. kubectl 概述

   kubectl 是 Kubernetes 集群的命令行工具，通过 kubectl 能够对集群本身进行管理，并能 够在集群上进行容器化应用的安装部署。

2. kubectl 命令的语法

   kubuectl [command] [type] [name] [flags]

   - comand：指定要对资源执行的操作，例如 create、get、describe 和 delet
   - TYPE：指定资源类型，资源类型是大小写敏感的，开发者能够以单数、复数和缩略的 形式。例如：
   - NAME：指定资源的名称，名称也大小写敏感的。如果省略名称，则会显示所有的资源
   - flags：指定可选的参数。例如，可用-s 或者–server 参数指定 Kubernetes API server 的地址和端口。

3. kubectl help 获取更多

   ```shell
   kubectl --help
   kubectl get --help
   ```

4. kubectl 子命令使用分

   - 基础命令

     | name    | desc                                                 |
     | ------- | ---------------------------------------------------- |
     | create  | 通过文件名或者标准输入创建资源                       |
     | expose  | 将一个资源公开为一个新的service                      |
     | run     | 在集群中运行一个特定的镜像                           |
     | set     | 在对象上设置特定的功能                               |
     | get     | 显示一个或多个资源                                   |
     | explain | 文档参考资料                                         |
     | edit    | 使用默认编辑器编辑一个资源                           |
     | delete  | 通过文件名、标准输入、资源名称或标签选择器来删除资源 |

   - 部署和集群管理命令

     | type         | name          | desc                                                 |
     | ------------ | ------------- | ---------------------------------------------------- |
     | 部署命令     | rollout       | 管理资源的发布                                       |
     |              | rolling-updte | 对给定的复制控制器滚动更新                           |
     |              | scale         | 扩容或缩容Pod数量，Deployment、ReplicaSet、RC、Job   |
     |              | autoscale     | 创建一个自动选择扩容或缩容并设置Pod数量              |
     | 集群管理命令 | certificate   | 修改证书资源                                         |
     |              | cluster-info  | 显示集群信息                                         |
     |              | top           | 显示资源（CPU/Memory/Storage）使用。需要Heapster运行 |
     |              | cordon        | 标记节点不可调度                                     |
     |              | uncordon      | 标记节点可调度                                       |
     |              | drain         | 驱逐节点上的应用，准备下线维护                       |
     |              | taint         | 修改节点taint标记                                    |

   - 故障调试命令

     | type               | name         | desc                                                         |
     | ------------------ | ------------ | ------------------------------------------------------------ |
     | 故障诊断和调试命令 | describe     | 显示特定资源或资源组的详细信息                               |
     |                    | logs         | 在一个pod中打印一个容器日志。如果pod只有一个容器，容器名称是可选的 |
     |                    | attach       | 附加到一个运行的容器                                         |
     |                    | exec         | 执行命令到容器                                               |
     |                    | port-forward | 转发一个或多个本地端口到一个pod                              |
     |                    | proxy        | 运行一个proxy到Kubernetes API server                         |
     |                    | cp           | 拷贝文件或目录到容器中                                       |
     |                    | auth         | 检查鉴权                                                     |

   - 其他命令

     | type     | name         | desc                                                |
     | -------- | ------------ | --------------------------------------------------- |
     | 高级命令 | apply        | 通过文件或标准输入对资源进行配置                    |
     |          | patch        | 使用补丁修改、更新资源字段                          |
     |          | replace      | 通过文件名或者标准输入替换一个资源                  |
     |          | convert      | 不同的API版本之间转换配置文件                       |
     | 设置命令 | label        | 更新资源上的标签                                    |
     |          | annotate     | 更新资源上的注释                                    |
     |          | completion   | 用于实现kubectl工具自动补全                         |
     | 其他命令 | api-versions | 打印支持的API版本                                   |
     |          | config       | 修改kubeconfig文件（用户访问API，比如配置认证信息） |
     |          | help         | 所有帮助命令                                        |
     |          | plugin       | 运行一个命令行插件                                  |
     |          | version      | 答应客户端和服务端版本信息                          |

     