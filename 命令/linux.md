## Linux 命令

### 用户

- 新增用户组

  ```shell
  groupadd dasheng
  ```

- 修改用户组

  ```shell
  groupmod –g 666 mu  #修改mu群组的GID为为666
  groupmod –n jack mu  #修改mu群组名为jack
  ```

- 删除用户组

  ```shell
  groupdel mu   #删除mu群组
  ```

  ```shell
  groupadd命令
  groupadd group1  #新增一个group1群组
  groupmod命令
  groupmod –g 666 mu  #修改mu群组的GID为为666
  groupmod –n jack mu  #修改mu群组名为jack
  groupdel命令
  groupdel mu   #删除mu群组
  ```

  

- 新增用户

  ```shell
  useradd -g dasheng tangwei
  ```

  ```shell
  u：指定用户的UID         useradd –u 1024 mu    #指定mu的UID为1024
  –g：指定用户所属的群组   useradd –g jack mu      #指定mu的所属群组为jack
  –d：指定用户的家目录      useradd –d /home/family mu  #指定mu家目录为 /home/family
  –c：指定用户的备注信息    useradd –c ‘mu_test’ mu  #mu的备注信息为’mu_test’
  –s：指定用户所用的shell
  –u：修改用户的UID      useradd –u 1024 mu    #修改mu的UID为1024
  –g：修改用户的GID      useradd –u 2048 mu    #修改mu的UID为2048
  –G：将一个用户加入到指定的群组中    usermod –G jack mu  #把mu加入jack群组中
  –d：修改用户的家目录   useradd –d /home/family mu  #修改mu家目录为 /home/family
  –c：修改用户的备注信息 useradd –c ‘mu_test’ mu  #修改mu的备注信息为’mu_test’
  –s：修改用户所用的shell
  –L：将用户的账号锁住   usermod –L mu  #锁定mu
  –U：将用户的账号解锁  usermod –U mu  #解锁mu
  ```

- 删除用户

  ```shell
  userdel mu      #删除mu用户不删除其家目录的命令
  userdel –r mu  #删除mu用户和其家目录的命令
  ```

- 常用命令

  ```shell
  1) 新建用户natasha，uid为1000，gid为555，备注信息为“master” 
  useradd -u 1000 -g 555 -c ‘master’ natasha
  2) 修改natasha用户的家目录为/Natasha
  usermod -d /Natasha natasha
  3) 查看用户信息配置文件的最后一行
  cat /etc/passwd
  shift + G
  4) 为natasha用户设置密码“123”
  echo 123 | passwd natasha 
  5) 查看用户密码配置文件的最后一行
  cat /etc/shadow
  shift + G
  6) 将natasha用户账户锁定
  usermod –L natasha
  7) 将natasha用户账户解锁
  usermod –U natasha
  8) 新建组police，gid为999
  groupadd -g 999 police
  9) 查看组配置文件的最后一行
  cat /etc/group
  shift + G
  10) 将natasha用户加入police组
  groupmod -G police natasha
  11) 修改police组的组名为jingcha
  groupmod -n jingcha police
  12) 删除natasha用户，连家目录和邮箱一起删除
  groupdel -r natasha
  13) 删除jingcha组
  groupdel jingcha
  ```

### 用户、组权限详解

- ls –l命令查看[文件](http://cppdaxue.com/tag/文件.html)权限

  d  rwx  rw-  r–

  第一个参数d为[目录](http://cppdaxue.com/tag/目录.html)[文件](http://cppdaxue.com/tag/文件.html)，此外还有其它符号 ： – 普通[文件](http://cppdaxue.com/tag/文件.html)

  第二个参数rwx 读 写 执行， – 表示没有权限的意思  u 所有者[用户](http://cppdaxue.com/tag/用户.html)

  第三个参数同上   g 组[用户](http://cppdaxue.com/tag/用户.html)

  第四个参数同上   o 其它[用户](http://cppdaxue.com/tag/用户.html)

  7(rwx)  6(rw-)  5(r-w)  4(r–)  3(-wx)  2(-w-)  1(–w)  0(—)

- 常用操作

  1) 在[用户](http://cppdaxue.com/tag/用户.html)的主[目录](http://cppdaxue.com/tag/目录.html)下创建[目录](http://cppdaxue.com/tag/目录.html)test，进入test创建空[文件](http://cppdaxue.com/tag/文件.html)file1

  cd /

  mkdir  /test

  cd /text

  touch file1

  2) 以长格式形式显示[文件](http://cppdaxue.com/tag/文件.html)信息，注意[文件](http://cppdaxue.com/tag/文件.html)的权限和所属[用户](http://cppdaxue.com/tag/用户.html)和组

  ll – dl /test/file1

  3) 为[文件](http://cppdaxue.com/tag/文件.html)file1设置权限，使其他[用户](http://cppdaxue.com/tag/用户.html)可以对此[文件](http://cppdaxue.com/tag/文件.html)进行写操作。

   chmod o=w /test/file1

  4) 查看设置结果，

  ll – dl /test/file1

  5) 取消同组[用户](http://cppdaxue.com/tag/用户.html)对[文件](http://cppdaxue.com/tag/文件.html)file1的读取权限，并查看设置结果。

  chmod g-r /test/file1

  ll – dl /test/file1

  6) 用数字表示法为[文件](http://cppdaxue.com/tag/文件.html)file设置权限，所有者可读、可写、可执行，所属组[用户](http://cppdaxue.com/tag/用户.html)和其他[用户](http://cppdaxue.com/tag/用户.html)只具有读和执行的权限。设置完成后查看设置结果。

  chmod 755 /test/file1

  ll – dl /test/file1

  7) 用数字形式更改[文件](http://cppdaxue.com/tag/文件.html)file1的权限，使所有者只能读取此[文件](http://cppdaxue.com/tag/文件.html)。其他任何[用户](http://cppdaxue.com/tag/用户.html)都没有权限。查看设置结果。

  chmod 400 /test/file1

  ll – dl /test/file1

  8) 回到上层[目录](http://cppdaxue.com/tag/目录.html)，查看test的权限

  cd..

  ll – dl /test

  9) 为其他[用户](http://cppdaxue.com/tag/用户.html)添加对此[目录](http://cppdaxue.com/tag/目录.html)的写权限

  chmod o+w /test