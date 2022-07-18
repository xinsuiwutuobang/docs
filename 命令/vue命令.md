## vue命令

### 常用命令

- 安装

  ```shell
  # 建议不要直接使用 cnpm 安装依赖，会有各种诡异的 bug。可以通过如下操作解决 npm 下载速度慢的问题
  # 删掉node_modules 多次执行
  npm install --registry=https://registry.npm.taobao.org
  ```

- 根据不同环境build

   ```java
   npm run build --dev  
   ```
  
  ​            

### 问题

- linux install出现：**ERR! stack Error: EACCES: permission denied, mkdir '/home/mydata/project/smartCity2-web/node_modules/node-sass**

  npm 出于安全考虑不支持以 root 用户运行，即使你用 root 用户身份运行了，npm 会自动转成一个叫 nobody 的用户来运行，而这个用户几乎没有任何权限。这样的话如果你脚本里有一些需要权限的操作，比如写文件（尤其是写 /root/.node-gyp），就会崩掉了。

  为了避免这种情况，要么按照 npm 的规矩来，专门建一个用于运行 npm 的高权限用户；要么加 --unsafe-perm 参数，这样就不会切换到 nobody 上，运行时是哪个用户就是哪个用户，即使是 root。

  改写命令

  ```shell
  npm install --registry=https://registry.npm.taobao.org --unsafe-perm
  ```

  