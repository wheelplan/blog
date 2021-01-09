---
title: Kubeadm 部署 kubernetes v1.18.2 集群（腾讯云版）
date: 2020-05-01 22:30:50
top_img: https://encrypted-tbn0.gstatic.com/images?q=tbn%3AANd9GcSA8EZEgZP9KYdrrMqkRcHYdhSEPOzZdjV0ju5zDKrW6XT6CGb-&usqp=CAU
keywords: kubeadm 部署 kubernetes 集群
tags:
 - kubernetes
---

# Kubeadm 部署 kubernetes v1.18.2 集群（腾讯云版）

## 主机规划

| 主机名     | IP 地址  | 推荐 CPU 和 内存 配置       |
| ---------- | -------- | --------------------------- |
| k8s-master | 10.0.0.3 | > 2 核 2 G (关闭 swap 分区) |
| k8s-node1  | 10.0.1.3 | > 2 核 2 G (关闭 swap 分区) |
| k8s-node2  | 10.0.2.3 | > 2 核 2 G (关闭 swap 分区) |

## 升级内核

```bash
# 导入 GPG 公钥，安装最新版内核（所有主机均操作）
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm
yum --disablerepo="*" --enablerepo="elrepo-kernel" list available
yum --enablerepo=elrepo-kernel install kernel-ml -y

# 设置 GRUB 默认启动项（所有主机均操作）
sed -i '/GRUB_DEFAULT=/c GRUB_DEFAULT=0' /etc/default/grub
grub2-mkconfig -o /boot/grub2/grub.cfg

# 重启（所有主机均操作）
init 6

# 清除旧内核【慎用】（所有主机均操作）
rpm -qa | grep kernel | grep -v kernel-ml* | xargs yum remove -y

# 更新所有软件（所有主机均操作）
yum update -y
```

## 配置 Host 解析

```bash
# 所有主机均操作
cat >> /etc/hosts << EOF
10.0.0.3 k8s-master
10.0.1.3 k8s-node1
10.0.2.3 k8s-node2
EOF
```

## 关闭 swap 分区

```bash
# 所有主机均操作
swapoff -a
sed -i '/swap/s/^/#/g' /etc/fstab
```

## 安装 Docker 引擎

```bash
# 部署 Docker（所有主机均操作）
wget -O /etc/yum.repos.d/docker-ce.repo https://download.docker.com/linux/centos/docker-ce.repo
sed -i 's+download.docker.com+mirrors.cloud.tencent.com/docker-ce+' /etc/yum.repos.d/docker-ce.repo
yum install docker-ce -y
systemctl enable docker && systemctl start docker

# 配置腾讯云 docker 镜像加速（所有主机均操作）
cat > /etc/docker/daemon.json << EOF
{
   "registry-mirrors": [
       "https://mirror.ccs.tencentyun.com"
  ]
}
EOF

systemctl daemon-reload && systemctl restart docker 
```

##  安装 Kubernetes 组件

```bash
# 配置 kubernetes 源（所有主机均操作）
cat > /etc/yum.repos.d/kubernetes.repo << EOF 
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

# 安装 kubelet kubeadm kubectl 组件（所有主机均操作）
yum install -y kubelet-1.18.2 kubeadm-1.18.2 kubectl-1.18.2
systemctl enable kubelet

# 配置内核模块相关参数（所有主机均操作）
cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables=1
net.bridge.bridge-nf-call-iptables=1
EOF

sysctl -p /etc/sysctl.d/k8s.conf

# 忽略使用 swap 分区的警告
# echo KUBELET_EXTRA_ARGS="--fail-swap-on=false" > /etc/sysconfig/kubelet
```

## Master 节点导入镜像

```bash
# 下载镜像（仅 Master 节点操作）
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver:v1.18.2
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-controller-manager:v1.18.2
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-scheduler:v1.18.2
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy:v1.18.2
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.2
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/etcd:3.4.3-0
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/coredns:1.6.7

# 重新打标（仅 Master 节点操作）
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver:v1.18.2 k8s.gcr.io/kube-apiserver:v1.18.2
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/kube-controller-manager:v1.18.2 k8s.gcr.io/kube-controller-manager:v1.18.2
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/kube-scheduler:v1.18.2 k8s.gcr.io/kube-scheduler:v1.18.2
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy:v1.18.2 k8s.gcr.io/kube-proxy:v1.18.2
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.2 k8s.gcr.io/pause:3.2
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/etcd:3.4.3-0 k8s.gcr.io/etcd:3.4.3-0
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/coredns:1.6.7 k8s.gcr.io/coredns:1.6.7

# 删除旧镜像（仅 Master 节点操作）
docker image rm registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver:v1.18.2
docker image rm registry.cn-hangzhou.aliyuncs.com/google_containers/kube-controller-manager:v1.18.2
docker image rm registry.cn-hangzhou.aliyuncs.com/google_containers/kube-scheduler:v1.18.2
docker image rm registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy:v1.18.2
docker image rm registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.2
docker image rm registry.cn-hangzhou.aliyuncs.com/google_containers/etcd:3.4.3-0
docker image rm registry.cn-hangzhou.aliyuncs.com/google_containers/coredns:1.6.7
```

## Worker 节点导入镜像

```bash
# 导入镜像（仅 Node1 和 Node2 节点操作）
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/coredns:1.6.7
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.2
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy:v1.18.2

# 重新打标（仅 Node1 和 Node2 节点操作）
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/coredns:1.6.7 k8s.gcr.io/coredns:1.6.7
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.2 k8s.gcr.io/pause:3.2
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy:v1.18.2 k8s.gcr.io/kube-proxy:v1.18.2

# 删除旧镜像（仅 Node1 和 Node2 节点操作）
docker image rm registry.cn-hangzhou.aliyuncs.com/google_containers/coredns:1.6.7
docker image rm registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.2
docker image rm registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy:v1.18.2
```

##  Kubeadm 初始化集群

```bash
# 初始化集群（仅 Master 节点操作）
kubeadm init --kubernetes-version=1.18.2 --pod-network-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/12 --ignore-preflight-errors=Swap --ignore-preflight-errors=NumCPU

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# 复制初始化完成后的最后 2 行提示内容在从节点上执行(分别在 node1 node2 上执行) 
# 请自行复制初始化完成后的最后 2 行提示内容从节点上执行，以下内容仅供参考
# kubeadm join 3.3.3.80:6443 --token 5u4vdz.7bbwnxltoeupxddp \
#    --discovery-token-ca-cert-hash sha256:4642090e953981bad15b1a5c1ec8cf8bdc32be681a7e348bfd5910e06fe8c2bb

# 安装 CNI 网络插件（仅 Master 节点操作）
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

## 查看集群信息

```bash
# Master
kubectl cluster-info
kubectl get nodes
kubectl get pods -n kube-system -o wide
```

## 添加命令自动补全

```shell
# Master
echo 'source <(kubectl completion bash)' >> /etc/profile.d/kubectl.sh
source /etc/profile
```

## 解决 Worker 节点宕掉需 5 分钟后才能被感知的问题

```bash
# 在 Master 节点配置中添加两项参数
vim /etc/kubernetes/manifests/kube-apiserver.yaml
    - --default-not-ready-toleration-seconds=5
    - --default-unreachable-toleration-seconds=5
```
