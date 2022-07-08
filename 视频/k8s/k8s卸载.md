## 卸载K8S

### 首先清理运行到k8s群集中的pod

```shell
kubectl delete node --all
```

### 使用脚本停止所有k8s服务

```shell
for service in kube-apiserver kube-controller-manager kubectl kubelet kube-proxy kube-scheduler; 
do
      systemctl stop $service
done
```

```shell
kubeadm reset -f

rm -rf ~/.kube/

rm -rf /etc/kubernetes/
 
rm -rf /etc/systemd/system/kubelet.service.d

rm -rf /etc/systemd/system/kubelet.service

rm -rf /usr/bin/kube*

rm -rf /etc/cni

rm -rf /opt/cni

rm -rf /var/lib/etcd

rm -rf /var/etcd

yum clean all

yum remove kube*
```

### 删除对应的K8S容器镜像

```shell
docker images
#删除所有镜像
docker rmi `docker images | awk '{print $3}'`
```

