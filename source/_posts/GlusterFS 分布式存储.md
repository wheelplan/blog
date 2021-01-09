---
title: GlusterFS 分布式存储
date: 2020-06-07 23:53:33
top_img: http://www.rayootech.com/images/ecom/fenbu/fenbu.png
keywords: GlusterFS glusterfs 分布式存储
tags:
 - glusterfs
 - 分布式存储
---

# GlusterFS 分布式存储系统

Gluster File System 是一个开源的分布式文件系统，是 Scale 存储的核心，能够处理千数量级的客户端。是整合了许多存储块（server）通过 Infiniband RDMA 或者 TCP/IP 方式互联的一个并行的网络文件系统。

## CentOS 7 安装 GlusterFS

官方参考文档：https://wiki.centos.org/SpecialInterestGroup/Storage/gluster-Quickstart

| 主机名 | IP       | 角色                       |
| ------ | -------- | -------------------------- |
| node1  | 10.0.0.3 | GlusterFS 节点，客户端节点 |
| node2  | 10.0.1.3 | GlusterFS 节点             |
| node3  | 10.0.2.3 | GlusterFS 节点             |

### 1. 安装服务（在所有 GlusterFS 节点和客户端操作）

```bash
# 安装所需的 YUM 源
yum install centos-release-gluster

# 安装 glusterfs 服务
yum install glusterfs-server

# 启动服务并设置为开机自启
systemctl enable glusterd && systemctl start glusterd
```

### 2. 配置集群可信池（在任一节点操作即可）

```bash
# 将其他节点添加到集群中（node1 操作）
gluster peer probe 10.0.1.3
gluster peer probe 10.0.2.3

# 检查集群状态
gluster peer status
```

### 3. 设置 GlusterFS 卷（在任一节点操作即可）

```bash
# 创建一个名称为 data_rep 的复制卷
gluster volume create data_rep replica 3 transport tcp 10.0.0.3:/replica 10.0.1.3:/replica 10.0.2.3:/replica force

# 启动 data_rep 复制卷
gluster volume start data_rep

# 查看所有卷的状态信息
gluster volume info
```

### 4. 客户端挂载测试

```bash
# 挂载任意节点 IP:卷名(node1 操作)
mount -t glusterfs 10.0.0.3:data_rep /mnt

# 在 /mnt 目录下创建 26 个文件(node1 操作)
touch /mnt/{a..z}

# 查看各节点 /replica 目录中是否存在这些文件（node1 node2 node3 查看）
ls /replica | wc -l
```



## 卷的种类

**1、分布式卷**（Distributed Volume）

  又称哈希卷，近似于 RAID0，文件没有分片，文件根据hash算法写入各个节点的硬盘上，优点是容量大，缺点是没冗余。

```bash
# 创建分布卷
gluster volume create data_glu transport tcp 10.0.0.3:/glu 10.0.1.3:/glu force
```

**2、复制卷**（Replicated Volume）

相当于 RAID1，复制的份数，决定集群的大小，通常与分布式卷或者条带卷组合使用，解决前两种存储卷的冗余缺陷。缺点是磁盘利用率低。  复本卷在创建时可指定复本的数量，通常为 2 或者 3，复本在存储时会在卷的不同 brick 上，因此有几个复本就必须提供至少多个 brick，当其中一台服务器失效后，可以从另一台服务器读取数据，因此复制 GlusterFS 卷提高了数据可靠性的同时，还提供了数据冗余的功能。


```bash
# 创建复制卷
gluster volume create data_rep replica 2 transport tcp 10.0.0.3:/replica 10.0.1.3:/replica force
```

**3、分布式复制卷**（Distributed Replicated Volume）

分布式复制卷结合了分布式和复制卷的特点，看起来类似 RAID10，但其实不同，RAID10 其实质是条带化，但分布式复制卷则没有。

```bash
# 创建分布式复制卷
gluster volume create test-volume replica 2 transport tcp \
server1:/exp1 server2:/exp2 server3:/exp3 server4:/exp4
```

**4、条带卷**（Striped Volume）

相当于 RAID0，文件是分片均匀写在各个节点的硬盘上的，优点是分布式读写，性能整体较好。缺点是没冗余，分片随机读写可能会导致硬盘 IOPS 饱和。

```bash
# 创建条带卷
volume create datavol3 stripe 2 transport tcp 10.0.0.3:/data3 10.0.1.3:/data3 force
```

**5、分布式条带卷**（Distributed Striped Volume）

当单个文件的体型十分巨大，客户端数量更多时，条带卷已经无法满足需求，此时将分布式与条带化结合起来是一个比较好的选择。其性能与服务器数量有关。

```bash
gluster volume create test-volume stripe 4 transport tcp \
server1:/exp1 server2:/exp2 server3:/exp3 server4:/exp4 \
server5:/exp5 server6:/exp6 server7:/exp7 server8:/exp8
```
