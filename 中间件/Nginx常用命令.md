## Nginx常用命令

- 测试配置文件

  ```shell
  /nginx/sbin/nginx -t
  ```

- 启动命令

  ```shell
  /nginx/sbin/nginx
  ```

- 停止命令

  ```shell
  /nginx/sbin/nginx -s stop
  ```

  ```shell
  /nginx/sbin/nginx -s quit
  ```

- 重启命令

  ```shell
  /nginx/sbin/nginx -s reload
  ```

- 查看进程

  ```shell
  ps -ef | grep nginx
  ```

- 平滑重启

  ```shell
  kill -HUP Nginx主进程号
  ```

  