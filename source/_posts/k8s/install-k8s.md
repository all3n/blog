---
layout: post
title: install kubernetes in china
date: 2019-09-10
tags:
    - kubernetes
---
# install kubernets in china
[官方文档](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)
## 1. 环境相关设置

1. 关闭swap
然后需要禁用swap文件，这是Kubernetes的强制步骤。实现它很简单，编辑/etc/fstab文件，注释掉引用swap的行 去掉开机挂载swap
如果不重启 可以sudo swapoff -a 可以直接关掉
对于禁用swap内存，主要是为了性能考虑，具体原因可以查看Github上的Issue：Kubelet/Kubernetes should work with Swap Enabled。
2. 关闭SELinux，修改SELINUX属性

#setenforce 0
#vi /etc/sysconfig/selinux
SELINUX=disabled

3. 设置iptables

#vi /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
vm.swappiness=0

同iptables
sysctl --system


## 2. install docker
[use aliyun docker repo](https://yq.aliyun.com/articles/110806)
```
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

## 3. install kubelets kubeadm kubectl
```bash
install_dep(){
    cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
    setenforce 0
    yum install -y kubelet kubeadm kubectl
    systemctl enable kubelet && systemctl start kubelet
}
```

## 4. 初始化master节点
首选配置pod network 
```
cat <<EOF> /etc/cni/net.d/10-flannel.conf
{"name":"cbr0","type":"flannel","delegate": {"isDefaultGateway": true}}
EOF
mkdir /usr/share/oci-umount/oci-umount.d -p
mkdir /run/flannel/
cat <<EOF> /run/flannel/subnet.env
FLANNEL_NETWORK=10.244.0.0/16
FLANNEL_SUBNET=10.244.0.1/24
FLANNEL_MTU=1450
FLANNEL_IPMASQ=true
EOF
```


初始化master集群
注意在国内一定要设置  --image-repository 不然默认gcr.io 拉不下来
推荐azure (支持gcr.io,quay.io)


### k8s.gcr.io
[微软azure镜像](http://mirror.azure.cn/help/gcr-proxy-cache.html)
= gcr.azk8s.cn/google_containers
= registry.aliyuncs.com/google_containers 

### quay.io
quay.azk8s.cn


gcr.io  镜像有几个 一个是阿里云，还有一个是微软azure(推荐azure)



```bash
kubeadm init --pod-network-cidr=10.244.0.0/16 --image-repository gcr.azk8s.cn/google_containers --kubernetes-version v1.15.3 

mkdir -p $HOME/.kube
sudo cp -i -f /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config


```

[安装网络pods 插件](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#pod-network) 有多种选择 这里使用是flannel
```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/62e44c867a2846fefb68bd5f178daf4da3095ccb/Documentation/kube-flannel.yml
```

[安装dashboard](https://github.com/kubernetes/dashboard/)
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
```

执行完会有一个join的命令用于新node加入集群 可以记下来
如果之后忘记可以在master节点
执行 kubeadm token create --print-join-command 获得


执行
kubectl get nodes  查看节点是否正常
查看日志
journalctl -f -u kubelet

# node
在子节点安装kubeadm,kubectl,kubelet后执行
master安装时候出现的join 命令
kubeadm join ip:port --token YOU_TOKEN --discovery-token-ca-cert-hash sha256:YOU_TOKEN_HASH
