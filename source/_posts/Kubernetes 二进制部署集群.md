---
title: 二进制部署 kubernetes 集群
date: 2020-01-01 13:14:21
top_img: https://encrypted-tbn0.gstatic.com/images?q=tbn%3AANd9GcQ9kT-v8h4jC0espKHN3EtHBmIeDcTycpSj1Gpzg9QeX2RmxB6c&usqp=CAU
keywords: 二进制部署 kubernetes 集群
tags:
 - kubernetes
---


## 部署单 Master 集群

[集群规划](https://www.notion.so/a0467bc248ea42d2a57b3bb340bef27b)

### 初始化服务器

```bash
# 1.关闭防火墙
systemctl disable firewalld && systemctl stop firewalld

# 2.关闭 selinux
sed -i '/^SELINUX=/c SELINUX=disabled' /etc/selinux/config
setenforce 0

# 3.关闭 swap 分区
swapoff -a
sed -i '/swap/s/^/#/g' /etc/fstab

# 4.配置主机名
hostnamectl set-hostname k8s-master     # master 主机执行
hostnamectl set-hostname k8s-node01     # node01 主机执行
hostnamectl set-hostname k8s-node02     # node02 主机执行

# 5.配置相互解析
cat >> /etc/hosts << EOF
172.16.1.10 k8s-master
172.16.1.2 k8s-node01
172.16.1.3 k8s-node02
EOF

# 6.配置时间同步
yum install chrony -y

sed -i "/^#allow/c allow 10.0.0.0/16" /etc/chrony.conf  # 仅 master 主机执行
sed -i "/^#local/c local stratum 10" /etc/chrony.conf   # 仅 master 主机执行

sed -i "/^server /s@^@#@g" /etc/chrony.conf         # 仅 node 主机执行
echo "server 172.16.1.10 iburst" >> /etc/chrony.conf    # 仅 node 主机执行

systemctl enable chronyd && systemctl start chronyd

chronyc sources     # 检查 node 主机是否同步成功
```

### 部署 docker

**所有主机**

```bash
# 部署 docker
wget -O /etc/yum.repos.d/docker-ce.repo <https://download.docker.com/linux/centos/docker-ce.repo>
sed -i 's+download.docker.com+mirrors.cloud.tencent.com/docker-ce+' /etc/yum.repos.d/docker-ce.repo
yum install docker-ce -y
systemctl enable docker && systemctl start docker

# 配置腾讯云 docker 镜像加速
cat > /etc/docker/daemon.json << EOF
{
   "registry-mirrors": [
       "<https://mirror.ccs.tencentyun.com>"
  ]
}
EOF

systemctl daemon-reload && systemctl restart docker 
```

### 部署 etcd

### 颁发证书

**k8s-master**

```bash
# 下载 cfssl 工具
wget <https://pkg.cfssl.org/R1.2/cfssl_linux-amd64>
wget <https://pkg.cfssl.org/R1.2/cfssljson_linux-amd64>
wget <https://pkg.cfssl.org/R1.2/cfssl-certinfo_linux-amd64>
chmod +x cfssl_linux-amd64 cfssljson_linux-amd64 cfssl-certinfo_linux-amd64
mv cfssl_linux-amd64 /usr/local/bin/cfssl
mv cfssljson_linux-amd64 /usr/local/bin/cfssljson
mv cfssl-certinfo_linux-amd64 /usr/local/bin/cfssl-certinfo

# 生成自签证书
mkdir -p /ssl/etcd
cd $_

cat > ca-csr.json << EOF
{
    "CN": "etcd CA",
    "key": {
        "algo": "rsa",
        "size": 2048
    },
    "names": [
        {
            "C": "CN",
            "L": "ShenZhen",
            "ST": "ShenZhen"
        }
    ]
}
EOF

cat > ca-config.json << EOF
{
  "signing": {
    "default": {
      "expiry": "87600h"
    },
    "profiles": {
      "www": {
         "expiry": "87600h",
         "usages": [
            "signing",
            "key encipherment",
            "server auth",
            "client auth"
        ]
      }
    }
  }
}
EOF

cat > server-csr.json << EOF
{
    "CN": "etcd",
    "hosts": [
    "172.16.1.10",
    "172.16.1.2",
    "172.16.1.3"
    ],
    "key": {
        "algo": "rsa",
        "size": 2048
    },
    "names": [
        {
            "C": "CN",
            "L": "ShenZhen",
            "ST": "ShenZhen"
        }
    ]
}
EOF


cfssl gencert -initca ca-csr.json | cfssljson -bare ca -
cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=www server-csr.json | cfssljson -bare server

# 检查是否生成了 4 个 pem 文件
ls *pem | wc -l
```

### 安装 etcd

**k8s-master**

```bash
# 下载 etcd
cd /tmp
wget <https://download.rocc.top/kubernetes/etcd/etcd-v3.3.17-linux-amd64.tar.gz>
tar xf etcd-v3.3.17-linux-amd64.tar.gz
mkdir /opt/etcd/{bin,cfg,ssl} -p
mv etcd-v3.3.17-linux-amd64/etcd* /opt/etcd/bin/
chmod +x /opt/etcd/bin/* 

# 创建 etcd 配置文件
cat > /opt/etcd/cfg/etcd.conf << EOF
#[Member]
ETCD_NAME="etcd-1"
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
ETCD_LISTEN_PEER_URLS="<https://172.16.1.10:2380>"
ETCD_LISTEN_CLIENT_URLS="<https://172.16.1.10:2379>"

#[Clustering]
ETCD_INITIAL_ADVERTISE_PEER_URLS="<https://172.16.1.10:2380>"
ETCD_ADVERTISE_CLIENT_URLS="<https://172.16.1.10:2379>"
ETCD_INITIAL_CLUSTER="etcd-1=https://172.16.1.10:2380,etcd-2=https://172.16.1.2:2380,etcd-3=https://172.16.1.3:2380"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
ETCD_INITIAL_CLUSTER_STATE="new"
EOF

# ETCD_NAME 节点名称
# ETCD_DATA_DIR 数据目录
# ETCD_LISTEN_PEER_URLS 集群通信监听地址
# ETCD_LISTEN_CLIENT_URLS 客户端访问监听地址

# ETCD_INITIAL_ADVERTISE_PEER_URLS 集群通告地址
# ETCD_ADVERTISE_CLIENT_URLS 客户端通告地址
# ETCD_INITIAL_CLUSTER 集群节点地址
# ETCD_INITIAL_CLUSTER_TOKEN 集群Token
# ETCD_INITIAL_CLUSTER_STATE 加入集群的当前状态，new是新集群，existing表示加入已有集群

# 配置 systemd 管理
cat > /usr/lib/systemd/system/etcd.service << 'EOF'
[Unit]
Description=Etcd Server
After=network.target
After=network-online.target
Wants=network-online.target

[Service]
Type=notify
EnvironmentFile=/opt/etcd/cfg/etcd.conf
ExecStart=/opt/etcd/bin/etcd \\
--name=${ETCD_NAME} \\
--data-dir=${ETCD_DATA_DIR} \\
--listen-peer-urls=${ETCD_LISTEN_PEER_URLS} \\
--listen-client-urls=${ETCD_LISTEN_CLIENT_URLS},<http://127.0.0.1:2379> \\
--advertise-client-urls=${ETCD_ADVERTISE_CLIENT_URLS} \\
--initial-advertise-peer-urls=${ETCD_INITIAL_ADVERTISE_PEER_URLS} \\
--initial-cluster=${ETCD_INITIAL_CLUSTER} \\
--initial-cluster-token=${ETCD_INITIAL_CLUSTER_TOKEN} \\
--initial-cluster-state=new \\
--cert-file=/opt/etcd/ssl/server.pem \\
--key-file=/opt/etcd/ssl/server-key.pem \\
--peer-cert-file=/opt/etcd/ssl/server.pem \\
--peer-key-file=/opt/etcd/ssl/server-key.pem \\
--trusted-ca-file=/opt/etcd/ssl/ca.pem \\
--peer-trusted-ca-file=/opt/etcd/ssl/ca.pem
Restart=on-failure
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
EOF

# 拷贝证书
cp /ssl/etcd/*pem /opt/etcd/ssl/
```

**k8s-master**

```bash
ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa >/dev/null 2>&1
ssh-copy-id k8s-node01
ssh-copy-id k8s-node02

# 将 etcd 发送到 node 节点
scp /usr/lib/systemd/system/etcd.service k8s-node01:/usr/lib/systemd/system/etcd.service
scp /usr/lib/systemd/system/etcd.service k8s-node02:/usr/lib/systemd/system/etcd.service
scp -r /opt/etcd k8s-node01:/opt
scp -r /opt/etcd k8s-node02:/opt
```

**k8s-node01**

```bash
# 修改 etcd 配置文件
cat > /opt/etcd/cfg/etcd.conf << EOF
ETCD_NAME="etcd-2"
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
ETCD_LISTEN_PEER_URLS="<https://172.16.1.2:2380>"
ETCD_LISTEN_CLIENT_URLS="<https://172.16.1.2:2379>"

#[Clustering]
ETCD_INITIAL_ADVERTISE_PEER_URLS="<https://172.16.1.2:2380>"
ETCD_ADVERTISE_CLIENT_URLS="<https://172.16.1.2:2379>"
ETCD_INITIAL_CLUSTER="etcd-1=https://172.16.1.10:2380,etcd-2=https://172.16.1.2:2380,etcd-3=https://172.16.1.3:2380"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
ETCD_INITIAL_CLUSTER_STATE="new"
EOF
```

**k8s-node02**

```bash
# 修改 etcd 配置文件
cat > /opt/etcd/cfg/etcd.conf << EOF
ETCD_NAME="etcd-3"
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
ETCD_LISTEN_PEER_URLS="<https://172.16.1.3:2380>"
ETCD_LISTEN_CLIENT_URLS="<https://172.16.1.3:2379>"

#[Clustering]
ETCD_INITIAL_ADVERTISE_PEER_URLS="<https://172.16.1.3:2380>"
ETCD_ADVERTISE_CLIENT_URLS="<https://172.16.1.3:2379>"
ETCD_INITIAL_CLUSTER="etcd-1=https://172.16.1.10:2380,etcd-2=https://172.16.1.2:2380,etcd-3=https://172.16.1.3:2380"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
ETCD_INITIAL_CLUSTER_STATE="new"
EOF
```

**启动 etcd** （依次启动三个节点）

```bash
systemctl enable etcd && systemctl start etcd

# 检查各节点健康状况
/opt/etcd/bin/etcdctl --ca-file=/opt/etcd/ssl/ca.pem --cert-file=/opt/etcd/ssl/server.pem --key-file=/opt/etcd/ssl/server-key.pem --endpoints="<https://172.16.1.10:2379>,<https://172.16.1.2:2379>,<https://172.16.1.3:2379>" cluster-health
```

### 部署 flannel 网络

**node 节点**

```bash
# 写入预定义子网段
cd /opt/etcd/ssl
/opt/etcd/bin/etcdctl \\
--ca-file=ca.pem --cert-file=server.pem --key-file=server-key.pem \\
--endpoints="<https://172.16.1.2:2379>,<https://172.16.1.3:2379>,<https://172.16.1.1:2379>" \\
set /coreos.com/network/config  '{ "Network": "172.17.76.0/16", "Backend": {"Type": "vxlan"}}'

# 查看
/opt/etcd/bin/etcdctl \\
--ca-file=ca.pem --cert-file=server.pem --key-file=server-key.pem \\
--endpoints="<https://172.16.1.2:2379>,<https://172.16.1.3:2379>,<https://172.16.1.1:2379>" \\
get /coreos.com/network/config


# 下载二进制包
mkdir /opt/kubernetes/{bin,cfg,ssl,logs} -p
cd /opt/kubernetes/bin
# wget <https://github.com/coreos/flannel/releases/download/v0.11.0/flannel-v0.11.0-linux-amd64.tar.gz>
wget <https://download.rocc.top/kubernetes/flannel/flannel-v0.11.0/flanneld>
wget <https://download.rocc.top/kubernetes/flannel/flannel-v0.11.0/mk-docker-opts.sh>
chmod +x /opt/kubernetes/bin/*

# 创建配置文件
cat > /opt/kubernetes/cfg/flanneld.conf << EOF
FLANNEL_OPTIONS="\\
--etcd-endpoints=https://172.16.1.2:2379,<https://172.16.1.3:2379> \\
--etcd-cafile=/opt/etcd/ssl/ca.pem -etcd-certfile=/opt/etcd/ssl/server.pem \\
--etcd-keyfile=/opt/etcd/ssl/server-key.pem"
EOF

# 配置 systemd 管理
cat > /usr/lib/systemd/system/flanneld.service << 'EOF'
[Unit]
Description=Flanneld overlay address etcd agent
After=network-online.target network.target
Before=docker.service

[Service]
Type=notify
EnvironmentFile=/opt/kubernetes/cfg/flanneld.conf
ExecStart=/opt/kubernetes/bin/flanneld --ip-masq $FLANNEL_OPTIONS
ExecStartPost=/opt/kubernetes/bin/mk-docker-opts.sh -k DOCKER_NETWORK_OPTIONS -d /run/flannel/subnet.env
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

# 配置 docker 启动指定子网段
cat > /usr/lib/systemd/system/docker.service << 'EOF'

[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target firewalld.service
Wants=network-online.target

[Service]
Type=notify
EnvironmentFile=/run/flannel/subnet.env
ExecStart=/usr/bin/dockerd $DOCKER_NETWORK_OPTIONS
ExecReload=/bin/kill -s HUP $MAINPID
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity
TimeoutStartSec=0
Delegate=yes
KillMode=process
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s

[Install]
WantedBy=multi-user.target
EOF

# 重启服务
systemctl daemon-reload
systemctl enable flanneld && systemctl start flanneld
systemctl restart docker

# 查看网络
ifconfig
```

### Master 节点部署组件

### 颁发证书

```bash
mkdir /ssl/k8s
cd $_

cat > ca-config.json << EOF
{
  "signing": {
    "default": {
      "expiry": "87600h"
    },
    "profiles": {
      "kubernetes": {
         "expiry": "87600h",
         "usages": [
            "signing",
            "key encipherment",
            "server auth",
            "client auth"
        ]
      }
    }
  }
}
EOF

cat > ca-csr.json << EOF
{
    "CN": "kubernetes",
    "key": {
        "algo": "rsa",
        "size": 2048
    },
    "names": [
        {
            "C": "CN",
            "L": "ShenZhen",
            "ST": "ShenZhen",
            "O": "k8s",
            "OU": "System"
        }
    ]
}
EOF

cfssl gencert -initca ca-csr.json | cfssljson -bare ca -


# 生成 apiserver 证书
cat > server-csr.json << EOF
{
    "CN": "kubernetes",
    "hosts": [
      "3.3.3.1",
      "127.0.0.1",
      "172.16.1.10",
      "kubernetes",
      "kubernetes.default",
      "kubernetes.default.svc",
      "kubernetes.default.svc.cluster",
      "kubernetes.default.svc.cluster.local"
    ],
    "key": {
        "algo": "rsa",
        "size": 2048
    },
    "names": [
        {
            "C": "CN",
            "L": "ShenZhen",
            "ST": "ShenZhen",
            "O": "k8s",
            "OU": "System"
        }
    ]
}
EOF

cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=kubernetes server-csr.json | cfssljson -bare server


# 生成 kube-proxy 证书
cat > kube-proxy-csr.json << EOF
{
  "CN": "system:kube-proxy",
  "hosts": [],
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "L": "ShenZhen",
      "ST": "ShenZhen",
      "O": "k8s",
      "OU": "System"
    }
  ]
}
EOF

cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=kubernetes kube-proxy-csr.json | cfssljson -bare kube-proxy


# 检查是否生成了 6 个 pem 文件
ls *pem | wc -l
```

### 部署 apiserver 组件

**master**

```bash
# 下载二进制文件
mkdir /opt/kubernetes/{bin,cfg,ssl,logs} -p
 cp /ssl/k8s/*pem /opt/kubernetes/ssl/
cd /opt/kubernetes/bin
wget <https://download.rocc.top/kubernetes/v1.17.0/kubernetes-server/server/bin/kube-apiserver>
wget <https://download.rocc.top/kubernetes/v1.17.0/kubernetes-server/server/bin/kube-scheduler>
wget <https://download.rocc.top/kubernetes/v1.17.0/kubernetes-server/server/bin/kube-controller-manager>
wget <https://download.rocc.top/kubernetes/v1.17.0/kubernetes-server/server/bin/kubectl>
chmod +x /opt/kubernetes/bin/*
cp /opt/kubernetes/bin/* /bin

# 创建 token 文件
echo 'a113e32020e784c521c13alan19lcan,kubelet-bootstrap,10001,"system:kubelet-bootstrap"' > /opt/kubernetes/cfg/token.csv 

# 第一列：随机字符串
# 第二列：用户名
# 第三列：UID
# 第四列：用户组

# 创建 apiserver 配置文件
cat > /opt/kubernetes/cfg/kube-apiserver.conf << 'EOF'
KUBE_APISERVER_OPTS="--logtostderr=true \\
--v=3 \\
--log-dir=/opt/kubernetes/logs \\
--etcd-servers=https://172.16.1.10:2379,<https://172.16.1.2:2379>,<https://172.16.1.3:2379> \\
--bind-address=172.16.1.10 \\
--secure-port=6443 \\
--advertise-address=172.16.1.10 \\
--allow-privileged=true \\
--service-cluster-ip-range=10.0.0.0/24 \\
--enable-admission-plugins=NamespaceLifecycle,LimitRanger,SecurityContextDeny,ServiceAccount,ResourceQuota,NodeRestriction \\
--authorization-mode=RBAC,Node \\
--enable-bootstrap-token-auth=true \\
--token-auth-file=/opt/kubernetes/cfg/token.csv \\
--service-node-port-range=30000-32767 \\
--kubelet-client-certificate=/opt/kubernetes/ssl/server.pem  \\
--kubelet-client-key=/opt/kubernetes/ssl/server-key.pem \\
--tls-cert-file=/opt/kubernetes/ssl/server.pem  \\
--tls-private-key-file=/opt/kubernetes/ssl/server-key.pem \\
--client-ca-file=/opt/kubernetes/ssl/ca.pem \\
--service-account-key-file=/opt/kubernetes/ssl/ca-key.pem \\
--etcd-cafile=/opt/etcd/ssl/ca.pem \\
--etcd-certfile=/opt/etcd/ssl/server.pem \\
--etcd-keyfile=/opt/etcd/ssl/server-key.pem \\
--audit-log-maxage=30 \\
--audit-log-maxbackup=3 \\
--audit-log-maxsize=100 \\
--audit-log-path=/opt/kubernetes/logs/k8s-audit.log"
EOF


# 参数说明：
# –logtostderr                      启用日志
# —v                                日志等级
# –etcd-servers etcd                集群地址
# –bind-address                     监听地址
# –secure-port https                安全端口
# –advertise-address                集群通告地址
# –allow-privileged                 启用授权
# –service-cluster-ip-range         Service 虚拟 IP 地址段
# –enable-admission-plugins         准入控制模块
# –authorization-mode           认证授权，启用 RBAC 授权和节点自管理
# –enable-bootstrap-token-auth  启用 TLS bootstrap 功能
# –token-auth-file              token 文件
# –service-node-port-range      Service Node 类型默认分配端口范围

# 配置 systemd 管理
cat > /usr/lib/systemd/system/kube-apiserver.service << 'EOF'
[Unit]
Description=Kubernetes API Server
Documentation=https://github.com/kubernetes/kubernetes
After=etcd.service
Wants=etcd.service

[Service]
EnvironmentFile=-/opt/kubernetes/cfg/kube-apiserver.conf
ExecStart=/opt/kubernetes/bin/kube-apiserver $KUBE_APISERVER_OPTS
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

# 启动服务
systemctl enable kube-apiserver && systemctl start kube-apiserver
```

### 部署 scheduler 组件

**master**

```bash
# 创建 scheduler 配置文件
cat > /opt/kubernetes/cfg/kube-scheduler.conf << 'EOF'
KUBE_SCHEDULER_OPTS="--logtostderr=true \\
--v=3 \\
--log-dir=/opt/kubernetes/logs \\
--master=127.0.0.1:8080 \\
--address=127.0.0.1 \\
--leader-elect"
EOF

# –master 连接本地 apiserver
# –leader-elect 当该组件启动多个时，自动选举（HA）

# 配置 systemd 管理
cat > /usr/lib/systemd/system/kube-scheduler.service << 'EOF'
[Unit]
Description=Kubernetes Scheduler
Documentation=https://github.com/kubernetes/kubernetes

[Service]
EnvironmentFile=-/opt/kubernetes/cfg/kube-scheduler.conf
ExecStart=/opt/kubernetes/bin/kube-scheduler $KUBE_SCHEDULER_OPTS
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

# 启动服务
systemctl enable kube-scheduler && systemctl start kube-scheduler
```

### 部署 controller-manager 组件

**master**

```bash
# 创建配置文件
cat > /opt/kubernetes/cfg/kube-controller-manager.conf << 'EOF'
KUBE_CONTROLLER_MANAGER_OPTS="--logtostderr=true \\
--v=5 \\
--log-dir=/opt/kubernetes/logs \\
--leader-elect=true \\
--master=127.0.0.1:8080 \\
--address=127.0.0.1 \\
--allocate-node-cidrs=true \\
--cluster-cidr=10.244.0.0/16 \\
--service-cluster-ip-range=10.0.0.0/24 \\
--cluster-name=kubernetes \\
--cluster-signing-cert-file=/opt/kubernetes/ssl/ca.pem \\
--cluster-signing-key-file=/opt/kubernetes/ssl/ca-key.pem  \\
--root-ca-file=/opt/kubernetes/ssl/ca.pem \\
--service-account-private-key-file=/opt/kubernetes/ssl/ca-key.pem \\
--experimental-cluster-signing-duration=87600h0m0s"
EOF

# 配置 systemd 管理
cat > /usr/lib/systemd/system/kube-controller-manager.service << 'EOF'
[Unit]
Description=Kubernetes Controller Manager
Documentation=https://github.com/kubernetes/kubernetes

[Service]
EnvironmentFile=-/opt/kubernetes/cfg/kube-controller-manager.conf
ExecStart=/opt/kubernetes/bin/kube-controller-manager $KUBE_CONTROLLER_MANAGER_OPTS
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

# 启动服务
systemctl enable kube-controller-manager && systemctl restart kube-controller-manager

# 检查
kubectl get cs
```

### Node 节点部署组件

**master**

```bash
# 将 kubelet-bootstrap 用户绑定到系统集群角色
kubectl create clusterrolebinding kubelet-bootstrap \\
  --clusterrole=system:node-bootstrapper \\
  --user=kubelet-bootstrap

cd /opt/kubernetes/ssl/
cat > kubernetes.sh << 'EOF'

# 创建 kubelet bootstrapping kubeconfig 
BOOTSTRAP_TOKEN=a113e32020e784c521c13alan19lcan
KUBE_APISERVER="<https://172.16.1.10:6443>"

# 设置集群参数
kubectl config set-cluster kubernetes \\
  --certificate-authority=./ca.pem \\
  --embed-certs=true \\
  --server=${KUBE_APISERVER} \\
  --kubeconfig=bootstrap.kubeconfig

# 设置客户端认证参数
kubectl config set-credentials kubelet-bootstrap \\
  --token=${BOOTSTRAP_TOKEN} \\
  --kubeconfig=bootstrap.kubeconfig

# 设置上下文参数
kubectl config set-context default \\
  --cluster=kubernetes \\
  --user=kubelet-bootstrap \\
  --kubeconfig=bootstrap.kubeconfig

# 设置默认上下文
kubectl config use-context default --kubeconfig=bootstrap.kubeconfig


# 创建 kube-proxy kubeconfig 文件
kubectl config set-cluster kubernetes \\
  --certificate-authority=./ca.pem \\
  --embed-certs=true \\
  --server=${KUBE_APISERVER} \\
  --kubeconfig=kube-proxy.kubeconfig

kubectl config set-credentials kube-proxy \\
  --client-certificate=./kube-proxy.pem \\
  --client-key=./kube-proxy-key.pem \\
  --embed-certs=true \\
  --kubeconfig=kube-proxy.kubeconfig

kubectl config set-context default \\
  --cluster=kubernetes \\
  --user=kube-proxy \\
  --kubeconfig=kube-proxy.kubeconfig
EOF

sh kubernetes.sh
scp bootstrap.kubeconfig kube-proxy.kubeconfig k8s-node01:/opt/kubernetes/cfg/
scp bootstrap.kubeconfig kube-proxy.kubeconfig k8s-node02:/opt/kubernetes/cfg/
scp ca.pem kube-proxy.pem kube-proxy-key.pem k8s-node01:/opt/kubernetes/ssl/
scp ca.pem kube-proxy.pem kube-proxy-key.pem k8s-node02:/opt/kubernetes/ssl/
```

### 部署 kubelet 组件

**node01**

```bash
cd /opt/kubernetes/bin/
wget <https://download.rocc.top/kubernetes/v1.17.0/kubernetes-server/server/bin/kubelet>
wget <https://download.rocc.top/kubernetes/v1.17.0/kubernetes-server/server/bin/kube-proxy>
chmod +x *

# 创建配置文件
cat > /opt/kubernetes/cfg/kubelet.config << EOF
kind: KubeletConfiguration
apiVersion: kubelet.config.k8s.io/v1beta1
address: 172.16.1.2
port: 10250
readOnlyPort: 10255
cgroupDriver: cgroupfs
clusterDNS: ["10.0.0.2"]
clusterDomain: cluster.local
failSwapOn: false
authentication:
  anonymous:
    enabled: true
EOF

cat > /opt/kubernetes/cfg/kubelet.conf << 'EOF'
KUBELET_OPTS="--logtostderr=true \\
--v=3 \\
--hostname-override=k8s-node01 \\
--kubeconfig=/opt/kubernetes/cfg/kubelet.kubeconfig \\
--bootstrap-kubeconfig=/opt/kubernetes/cfg/bootstrap.kubeconfig \\
--config=/opt/kubernetes/cfg/kubelet.config \\
--cert-dir=/opt/kubernetes/ssl \\
--pod-infra-container-image=registry.cn-hangzhou.aliyuncs.com/google-containers/pause-amd64:3.0"
EOF

# 参数说明
# –hostname-override            在集群中显示的主机名
# –kubeconfig                   指定 kubeconfig 文件位置，会自动生成
# –bootstrap-kubeconfig         指定刚才生成的 bootstrap.kubeconfig 文件
# –cert-dir                     颁发证书存放位置
# –pod-infra-container-image    管理 Pod 网络的镜像

# 配置 systemd 管理
cat > /usr/lib/systemd/system/kubelet.service << 'EOF'
[Unit]
Description=Kubernetes Kubelet
After=docker.service
Requires=docker.service

[Service]
EnvironmentFile=/opt/kubernetes/cfg/kubelet.conf
ExecStart=/opt/kubernetes/bin/kubelet $KUBELET_OPTS
Restart=on-failure
KillMode=process

[Install]
WantedBy=multi-user.target
EOF

# 启动服务
systemctl enable kubelet && systemctl start kubelet
```

**node02**

```bash
cd /opt/kubernetes/bin/
wget <https://download.rocc.top/kubernetes/v1.17.0/kubernetes-server/server/bin/kubelet>
wget <https://download.rocc.top/kubernetes/v1.17.0/kubernetes-server/server/bin/kube-proxy>
chmod +x *

# 创建配置文件
cat > /opt/kubernetes/cfg/kubelet.config << EOF
kind: KubeletConfiguration
apiVersion: kubelet.config.k8s.io/v1beta1
address: 172.16.1.3
port: 10250
readOnlyPort: 10255
cgroupDriver: cgroupfs
clusterDNS: ["10.0.0.2"]
clusterDomain: cluster.local
failSwapOn: false
authentication:
  anonymous:
    enabled: true
EOF

cat > /opt/kubernetes/cfg/kubelet.conf << 'EOF'
KUBELET_OPTS="--logtostderr=true \\
--v=3 \\
--log-dir=/opt/kubernetes/logs \\
--network-plugin=cni \\
--hostname-override=k8s-node02 \\
--kubeconfig=/opt/kubernetes/cfg/kubelet.kubeconfig \\
--bootstrap-kubeconfig=/opt/kubernetes/cfg/bootstrap.kubeconfig \\
--config=/opt/kubernetes/cfg/kubelet.config \\
--cert-dir=/opt/kubernetes/ssl \\
--pod-infra-container-image=registry.cn-hangzhou.aliyuncs.com/google-containers/pause-amd64:3.0"
EOF

# 参数说明
# –hostname-override            在集群中显示的主机名
# –kubeconfig                   指定 kubeconfig 文件位置，会自动生成
# –bootstrap-kubeconfig         指定刚才生成的 bootstrap.kubeconfig 文件
# –cert-dir                     颁发证书存放位置
# –pod-infra-container-image    管理 Pod 网络的镜像

# 配置 systemd 管理
cat > /usr/lib/systemd/system/kubelet.service << 'EOF'
[Unit]
Description=Kubernetes Kubelet
After=docker.service
Requires=docker.service

[Service]
EnvironmentFile=/opt/kubernetes/cfg/kubelet.conf
ExecStart=/opt/kubernetes/bin/kubelet $KUBELET_OPTS
Restart=on-failure
KillMode=process

[Install]
WantedBy=multi-user.target
EOF

# 启动服务
systemctl enable kubelet && systemctl start kubelet
```

**master**

```bash
kubectl certificate approve `kubectl get csr | awk 'NR>1{print $1}'`
```

### 部署 kube-proxy 组件

**node01**

```bash
# 创建 kube-proxy 配置文件
cat > /opt/kubernetes/cfg/kube-proxy.conf << 'EOF'
KUBE_PROXY_OPTS="--logtostderr=true \\
--v=4 \\
--hostname-override=172.16.1.2 \\
--cluster-cidr=10.0.0.0/24 \\
--kubeconfig=/opt/kubernetes/cfg/kube-proxy.kubeconfig"
EOF

# 配置 systemd 管理 Kube-proxy 组件
cat > /usr/lib/systemd/system/kube-proxy.service << 'EOF'
[Unit]
Description=Kubernetes Proxy
After=network.target

[Service]
EnvironmentFile=-/opt/kubernetes/cfg/kube-proxy.conf
ExecStart=/opt/kubernetes/bin/kube-proxy $KUBE_PROXY_OPTS
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

# 启动服务
systemctl enable kube-proxy && systemctl start kube-proxy
```

**node02**

```bash
# 创建 kube-proxy 配置文件
cat > /opt/kubernetes/cfg/kube-proxy.conf << 'EOF'
KUBE_PROXY_OPTS="--logtostderr=true \\
--v=4 \\
--hostname-override=172.16.1.3 \\
--cluster-cidr=10.0.0.0/24 \\
--kubeconfig=/opt/kubernetes/cfg/kube-proxy.kubeconfig"
EOF

# 配置 systemd 管理 Kube-proxy 组件
cat > /usr/lib/systemd/system/kube-proxy.service << 'EOF'
[Unit]
Description=Kubernetes Proxy
After=network.target

[Service]
EnvironmentFile=-/opt/kubernetes/cfg/kube-proxy.conf
ExecStart=/opt/kubernetes/bin/kube-proxy $KUBE_PROXY_OPTS
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

# 启动服务
systemctl enable kube-proxy && systemctl start kube-proxy
```

### 配置网络

**node**

```bash
wget <https://download.rocc.top/kubernetes/cni/cni-plugins-linux-amd64-v0.8.5.tgz>
mkdir -p /opt/cni/bin /etc/cni/net.d/
tar xf cni-plugins-linux-amd64-v0.8.5.tgz -C /opt/cni/bin/
```

***master\***

```bash
kubectl apply -f <https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml>
```

**测试**

```
kubectl run nginx --image=nginx --replicas=3
kubectl expose deployment nginx --port=80 --type=NodePort
kubectl get svc
```

### 安装 kuboard 可视化界面

**master**

```bash
kubectl apply -f <https://kuboard.cn/install-script/kuboard.yaml>

# 查看运行状态
kubectl get pods -l k8s.eip.work/name=kuboard -n kube-system

# 获取 token
kubectl -n kube-system get secret $(kubectl -n kube-system get secret | grep kuboard-user | awk '{print $1}') -o go-template='{{.data.token}}' | base64 -d

# 查看暴露的端口
kubectl get svc -n kube-system

# 浏览器访问任意 node 节点的 IP:暴露的端口
```

### 安装 coredns

```bash
git clone <https://github.com/coredns/deployment.git>
cd deployment/kubernetes
yum install jq -y
./deploy.sh -i 3.3.3.254 > coredns.yaml
kubectl apply -f coredns.yaml
```
