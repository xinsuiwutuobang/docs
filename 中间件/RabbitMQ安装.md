## 安装及配置

> 提供Windows和Linux两种安装方式

### Windows下的安装

- 安装Erlang，下载地址：http://erlang.org/download/otp_win64_21.3.exe

![](E:\yangfei\技术总结\images\erlang.png)

- 安装RabbitMQ，下载地址：https://dl.bintray.com/rabbitmq/all/rabbitmq-server/3.7.14/rabbitmq-server-3.7.14.exe

![](E:\yangfei\技术总结\images\rabbitmq.png)

- 安装完成后，进入RabbitMQ安装目录下的sbin目录

  ![](E:\yangfei\技术总结\images\rabbitmq_bin.png)

- 在该地址栏启动命令行，输入一下命令启动管理功能

  ```
  rabbitmq-plugins enable rabbitmq_management
  ```

  ![](E:\yangfei\技术总结\images\rabbitmq_management.png)

### Linux下的安装

- 下载rabbitmq 3.7.15的docker镜像；

  ```
  docker pull rabbitmq:3.7.15
  ```

- 使用docker命令启动服务；

  ```
  docker run -p 5672:5672 -p 15672:15672 --name rabbitmq \
  -d rabbitmq:3.7.15
  ```

- 进入容器并开启功能；

  ```
  docker exec -it rabbitmq /bin/bash
  rabbitmq-plugins enable rabbitmq_management
  ```

  ![](E:\yangfei\技术总结\images\linux_rabbitmq_management.png)

- 开启防火墙便于外网访问。

  ```
  firewall-cmd --zone=public --add-port=15672/tcp --permanent
  firewall-cmd --zone=public --add-port=5672/tcp --permanent
  firewall-cmd --reload
  ```

  

### 访问及配置

- 访问RabbitMQ管理页面地址，查看是否安装成功（Linux下使用服务器IP访问即可）：http://localhost:15672/

  ![](E:\yangfei\技术总结\images\15672.png)

- 输入账号密码并登录，这里使用默认账号密码登录：guest guest

- 创建帐号并设置其角色为管理员：mall mall

  ![](E:\yangfei\技术总结\images\user_mall.png)

- 创建一个新的虚拟host为：/mall

  ![](E:\yangfei\技术总结\images\host_mall.png)

- 点击mall用户进入用户配置页面；

  ![](E:\yangfei\技术总结\images\mall_user.png)

- 给mall用户配置该虚拟host的权限；

  ![](E:\yangfei\技术总结\images\rabbit_11.png)

- 至此，RabbitMQ的配置完成。

### 插件安装

- 管理功能插件

  ```java
  docker exec -it rabbitmq /bin/bash
  rabbitmq-plugins enable rabbitmq_management
  ```

- mqtt插件

  ```java
  #Linux
  docker exec -it rabbitmq /bin/bash
  rabbitmq-plugins enable rabbitmq_mqtt
  #windows
  pwd
  /d/utils/code_utils/RabbitMQ Server/rabbitmq_server-3.7.14/sbin
  ./rabbitmq-plugins.bat enable rabbitmq_mqtt
  ```

- mqtt web 插件

  ```java
  #Linux
  docker exec -it rabbitmq /bin/bash
  rabbitmq-plugins enable rabbitmq_web_mqtt
  #windows
  pwd
  /d/utils/code_utils/RabbitMQ Server/rabbitmq_server-3.7.14/sbin
  ./rabbitmq-plugins.bat enable rabbitmq_web_mqtt
  ```

  