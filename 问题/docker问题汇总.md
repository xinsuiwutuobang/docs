## Docker问题汇总

### docker容器无法访问宿主机报出 No route to host

- 服务连接404，通过通过查看nginx日志access.log, 确认upstream最终地址，地址没有问题，进入nginx容器调用该地址

  ```java
  2022/06/21 10:29:47 [error] 7#7: *164 connect() failed (113: No route to host) while connecting to upstream, client: 192.168.26.252, server: localhost, request: "GET /api/blade-auth/oauth/captcha HTTP/1.1", upstream: "http://10.10.30.92:80/blade-auth/oauth/captcha", host: "10.10.30.92:800"
  ```

- 进入nginx容器，测试方向代理请求地址,提示 failed: No route to host.

  ```java
  docker exec -it nginx /bin/bash ;
  apt update
  //wget
  apt install wget
  ```

  ```java
  root@36667018c0f9:/# wget http://10.10.30.92:80/blade-auth/oauth/captcha
  converted 'http://10.10.30.92:80/blade-auth/oauth/captcha' (ANSI_X3.4-1968) -> 'http://10.10.30.92:80/blade-auth/oauth/captcha' (UTF-8)
  --2022-06-21 10:35:18--  http://10.10.30.92/blade-auth/oauth/captcha
  Connecting to 10.10.30.92:80... failed: No route to host.
  ```

- 原因分析

  ​		启动docker时，docker进程会创建一个名为docker0的虚拟网桥，用于宿主机与容器之间的通信。当启动一个docker容器时，docker容器将会附加到虚拟网桥上，容器内的报文通过docker0向外转发。

  ​		如果docker容器访问宿主机，那么docker0网桥将报文直接转发到本机，报文的源地址是docker0网段的地址。而如果docker容器访问宿主机以外的机器，docker的SNAT网桥会将报文的源地址转换为宿主机的地址，通过宿主机的网卡向外发送。

   		因此，当docker容器访问宿主机时，如果宿主机服务端口会被防火墙拦截，从而无法连通宿主机，出现No route to host的错误。

  而访问宿主机所在局域网内的其他机器，由于报文的源地址是宿主机ip，因此，不会被目的机器防火墙拦截，所以可以访问。

- 解决方法

  - 方法一：在防火墙上开发指定端口

    ```java
    firewall-cmd --zone=public --add-port=80/tcp --permanent
    firewall-cmd --reload
    ```

  - 方法二：关闭防火墙

    ```java
    systemctl stop firewalld
    ```

