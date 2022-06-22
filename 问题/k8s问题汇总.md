## k8s问题汇总

### 文件系统无法删除

- 查看挂载请求

  ```shell
  cat /proc/mounts |grep "docker"
  ```

- ```shell
  [root@kmaster:/var/lib/kubelet]# rm -rf *
  rm: 无法删除"pods/3d7cb355-8f07-4cf7-926c-5b98db1961e4/volumes/kubernetes.io~secret/kube-proxy-token-fxsgm": 设备或资源忙
  rm: 无法删除"pods/ea985cae-c104-4c14-a734-8f528a3a117a/volumes/kubernetes.io~secret/coredns-token-826lj": 设备或资源忙
  rm: 无法删除"pods/521b5bad-7e7d-4164-8c1f-5519bb6ed620/volumes/kubernetes.io~secret/calico-kube-controllers-token-j28rd": 设备或资源忙
  [root@kmaster:/var/lib/kubelet]# df -h
  文件系统                 容量  已用  可用 已用% 挂载点
  /dev/mapper/centos-root   50G  6.1G   44G   13% /
  devtmpfs                 3.9G     0  3.9G    0% /dev
  tmpfs                    3.9G     0  3.9G    0% /dev/shm
  tmpfs                    3.9G  441M  3.4G   12% /run
  tmpfs                    3.9G     0  3.9G    0% /sys/fs/cgroup
  /dev/sda1               1014M  142M  873M   14% /boot
  /dev/mapper/centos-home   42G   48M   42G    1% /home
  tmpfs                    3.9G     0  3.9G    0% /var/lib/kubelet/pods/ea985cae-c104-4c14-a734-8f528a3a117a/volumes/kubernetes.io~secret/coredns-token-826lj
  tmpfs                    3.9G     0  3.9G    0% /var/lib/kubelet/pods/3d7cb355-8f07-4cf7-926c-5b98db1961e4/volumes/kubernetes.io~secret/kube-proxy-token-fxsgm
  tmpfs                    3.9G     0  3.9G    0% /var/lib/kubelet/pods/521b5bad-7e7d-4164-8c1f-5519bb6ed620/volumes/kubernetes.io~secret/calico-kube-controllers-token-j28rd
  tmpfs                    783M     0  783M    0% /run/user/0
  
  ```

- 卸载挂载目录

  ```shell
  [root@kmaster:/var/lib/kubelet]# umount /var/lib/kubelet/pods/ea985cae-c104-4c14-a734-8f528a3a117a/volumes/kubernetes.io~secret/coredns-token-826lj
  [root@kmaster:/var/lib/kubelet]# df -h
  文件系统                 容量  已用  可用 已用% 挂载点
  /dev/mapper/centos-root   50G  6.1G   44G   13% /
  devtmpfs                 3.9G     0  3.9G    0% /dev
  tmpfs                    3.9G     0  3.9G    0% /dev/shm
  tmpfs                    3.9G  441M  3.4G   12% /run
  tmpfs                    3.9G     0  3.9G    0% /sys/fs/cgroup
  /dev/sda1               1014M  142M  873M   14% /boot
  /dev/mapper/centos-home   42G   48M   42G    1% /home
  tmpfs                    3.9G     0  3.9G    0% /var/lib/kubelet/pods/3d7cb355-8f07-4cf7-926c-5b98db1961e4/volumes/kubernetes.io~secret/kube-proxy-token-fxsgm
  tmpfs                    3.9G     0  3.9G    0% /var/lib/kubelet/pods/521b5bad-7e7d-4164-8c1f-5519bb6ed620/volumes/kubernetes.io~secret/calico-kube-controllers-token-j28rd
  tmpfs                    783M     0  783M    0% /run/user/0
  
  ```

- 删除目录

  ```shell
  drwxr-x---. 5 root root 138 6月  22 15:03 pods
  [root@kmaster:/var/lib/kubelet]# rm -rf *
  [root@kmaster:/var/lib/kubelet]# ll
  总用量 0
  ```

  