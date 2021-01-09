---
title: Docker 基础入门
date: 2019-10-01 23:55:50
top_img: https://encrypted-tbn0.gstatic.com/images?q=tbn%3AANd9GcTiT97jtws6ahmVlozMvxB675jSM4aAvp5shhMP9NZNCpta8Ivh&usqp=CAU
keywords: docker 基础 入门 容器
tags:
 - docker
---


# Docker 简介

### Docker 是什么

简单地讲，可以将 Docker 容器理解为一种轻量级的沙盒（ sandbox）。 每个容器内运行着一个或多个应用，不同的容器相互隔离，容器之间也可以通过网络互相通信。容器的创建和停止十分快速，几乎跟创建和终止原生应用一致；另外，容器自身对系统资源的额外需求也十分有限，远远低于传统虚拟机。很多时候，甚至直接把容器当作应用本身也没有任何问题。

### Docker 的好处

以服务器迁移为例，Docker 提供了一种更为聪明的方式，通过容器来打包应用、解耦应用和运行平台。这意味着迁移的时候，只需要在新的服务器上启动需要的容器就可以了，无论新旧服务器是否是同一类型的平台。这无疑将帮助我们节约大量的宝贵时间，并降低部署过程出现问题的风险。

### Docker 在开发和运维中的优势

- 更快速的交付和部署

  使用 Docker ，开发人员可以使用镜像来快速构建一套标准的开发环境；开发完成之后，测试和运维人员可以直接使用完全相同的环境来部署代码。只要是开发测试过的代码，就可以确保在生产环境无缝运行。Docker 可以快速创建和删除容器，实现快速迭代，节约开发、测试、部署的大量时间。并且，整个过程全程可见，使团队更容易理解应用的创建和工作过程。

- 更高效的资源利用

  运行 Docker 容器不需要额外的虚拟化管理程序（ Virtual MachineManager, VMM ，以及 Hypervisor ）的支持， Docker 是内核级的虚拟化，可以实现更高的性能，同时对资源的额外需求很低。与传统虚拟机方式相比， Docker 的性能要提高 1 ~ 2 个数量级。

- 更轻松的迁移和扩展

  Docker 容器几乎可以在任意的平台上运行，包括物理机、虚拟机、公有云、私有云、个人电脑、 服务器等，同时支持主流的操作系统发行版本。这种兼容性让用户可以在不同平台之间轻松地迁移应用。

- 更简单的更新管理

  使用 Dockerfile ，只需要小小的配置修改，就可以替代以往大量的更新工作。所有修改都以增量的方式被分发和更新，从而实现自动化并且高效的容器管理。

### Docker 核心概念

1. Docker 镜像

Docker 镜像类似于虚拟机镜像，可以将它理解为一个只读的模板。

例如，一个镜像可以包含一个基本的操作系统环境，里面仅安装了 Apache 应用程序（或用户需要的其他软件） 可以把它称为一个 Apache 镜像。

镜像是创建 Docker 容器的基础。

通过版本管理和增量的文件系统， Docker 提供了一套十分简单的机制来创建和更新现有的镜像，用户甚至可以从网上下载一个已经做好的应用镜像，并直接使用。

2. Docker 容器

Docker 容器类似于一个轻量级的沙箱， Docker 利用容器来运行和隔离应用容器是从镜像创建的应用运行实例 它可以启动、开始、停止、删除，而这些容器都是彼此相互隔离、互不可见的。

可以把容器看作一个简易版的 Linux 系统环境（包括 root 用户权限、进程空间、用户空间和网络空间等）以及运行在其中的应用程序打包而成的盒子。

镜像自身是只读的。容器从镜像启动的时候，会在镜像的最上层创建一个可写层。

3. Docker 仓库

Docker 仓库类似于代码仓库，是 Docker 集中存放镜像文件的场所。

根据所存储的镜像公开分享与否， Docker 仓库可以分为公开仓库（Public ）和私有仓库（ Private ）两种形式。

目前，最大的公开仓库是官方提供的 Docker Hub ，其中存放着数量庞大的镜像供用户下国内不少云服务提供商（如腾讯云 阿里云等）也提供了仓库的本地源，可以提供稳定的国内访问。

当然，用户如果不希望公开分享自己的镜像文件， Docker 也支持用户在本地网络内创建一个只能自己访问的私有仓库。

当用户创建了自己的镜像之后就可以使用 push 命令将它上传到指定的公有或者私有仓库。这样用户下次在另外一台机器上使用该镜像时，只需要将其从仓库上 pull 下来就可以了。



# 安装 Docker 引擎

## Centos 7 环境下安装 Docker

```bash
# 下载官方源
wget -O /etc/yum.repos.d/docker-ce.repo <https://download.docker.com/linux/centos/docker-ce.repo>

# 替换为腾讯源
sed -i 's+download.docker.com+mirrors.cloud.tencent.com/docker-ce+' /etc/yum.repos.d/docker-ce.repo

# yum 安装
yum install docker-ce -y

# 启动 docker 并设置为开机自启
systemctl start docker && systemctl enable docker

# 配置腾讯云 docker 镜像加速
cat > /etc/docker/daemon.json << EOF
{
   "registry-mirrors": [
       "https://mirror.ccs.tencentyun.com"
  ]
}
EOF

systemctl daemon-reload && systemctl restart docker
```



# 基本的 Docker 镜像和容器管理命令

### 搜索 docker hub 仓库中的镜像

```bash
docker search nginx
```

docker search 镜像名称

-s 数值 ：只显示 STARS 达到指定 数值 以上的镜像。

### 获取指定版本的镜像

```bash
docker pull nginx:latest
```

docker pull 镜像名称：镜像版本

如果不添加镜像版本，默认会获取最新版本的镜像。

### 查看已下载的镜像

```bash
docker images
```

-q ：只显示镜像 ID

--no-trunc ：不截断内容显示，显示各个参数的全部信息

### 删除已下载的镜像

```bash
docker rmi nginx
```

docker rmi ”镜像名称 或 镜像 ID“

如果有基于该镜像的容器，则需要先删除相关容器后，再进行删除镜像操作。

### 运行一个 Nginx 容器

```bash
docker run -d -p 81:80 -v /docker/nginx:/usr/share/nginx/html --name web nginx
```

docker run -d -p 宿主机端口：容器端口 -v 宿主机目录：容器目录 —name 容器别名 镜像名称

-d ：后台运行

-p ：宿主机端口：容器端口，将容器的某个端口映射到宿主机的某个端口

-v ：宿主机目录：容器目录，将容器的某个目录挂载到宿主机的某个目录

-e ：当启动容器时传递环境变量及其值

—name ：容器别名

—rm ：容器在终止后会立刻删除。—rm 和 -d 参数不能同时使用。

### 查看所有运行中的容器

```bash
docker ps
```

-a ：查看所有容器

-q ：只显示容器 ID

--no-trunc ：不截断内容显示，显示各个参数的全部信息

### 查看容器详细信息（容器 Id 创建时间、路径、状态、镜像、配置等）

```bash
docker inspect 453cbf7a755a
```

docker inspect ”容器 ID 或 容器别名”

### 查看容器日志

```bash
docker logs web
```

docker logs “容器名称 或 容器 ID”

### 查看容器（在宿主机上的）进程

```bash
docker top web
```

docker top “容器名称 或 容器 ID”

### 显示 CPU 、内存、存储、网络等使用情况的统计信息

```bash
docker stats
```

-a ：输出所有容器统计信息，默认仅在运行中

### 复制本地文件到容器

```bash
docker cp index.html modest_mendel:/usr/share/nginx/html/index.html
```

docker cp 本地文件 "容器 ID 或容器别名":容器目标路径

－ a ：复制文件会带有原始的 uid/gid 信息

－ L ：当原路径为软连接时，默认只复制链接信息，使用该选项会复制链接的目标内容

### 查看容器内的数据变更情况

```bash
docker diff modest_mendel
```

docker diff  "容器 ID 或容器别名"

### 查看容器的端口映射

```bash
docker port modest_mendel
```

docker port "容器 ID 或容器别名"

### 访问容器内部系统

```bash
docker exec -it web bash
```

docker exec -it "容器 ID 或容器别名" "容器内部系统中的 shell"

### 更新容器运行时的配置

```bash
# 限制总配额为 1 秒，容器 test 所占用时间为 10% 
docker update --cpu-quota 1000000 modest_mendel
docker update --cpu-period 100000 modest_mendel
```

－blkio-weight uintl6 ：更新块 IO 限制， 10 ~ 1000 ，默认值为 0，代表无限制；

－ cpu-period int ：限制 CPU 调度器 CFS (Completely Fair Scheduler ）使用时间，单位为微秒，最小 1000;

－ cpu-quota int ：限制 CPU 调度器 CFS 配额，单位为微秒，最小 1000;

－ cpu-rt period int ：限制 CPU 调度器的实时周期，单位为微秒;

－ cpu-rt runtime int ：限制 CPU 调度器的实时运行时，单位为微秒；

－ c, -cpu-shares int 限制 CPU 使用份额；

－ cpus decimal ：限制 CPU 个数；

－ cpuset-cpus string ：允许使用的 CPU 核，如 0-3, 0,1;

－ cpuset mems string ：允许使用的内存块，如 0-3, 0, 1;

－ kernel-memor bytes ：限制使用的内核内存；

－ m, -memory bytes ：限制使用的内存；

－memory-reservation bytes ：内存软限制；

－memory-swap bytes ：内存加上缓存区的限制，-1 表示为对缓冲区无限制；

－ restart string ：容器退出后的重启策略。

### 停止容器

```bash
docker stop web
```

docker stop “容器名称 或 容器 ID”

### 启动容器

```bash
docker start web
```

docker start “容器名称 或 容器 ID”

### 重启容器

```bash
docker restart web
```

docker restart “容器名称 或 容器 ID”

### 删除容器

```bash
docker rm web
```

docker rm “容器名称 或 容器 ID”

-f ：强制删除。rm 不能删除正在运行的容器，加上 -f 参数可强制删除正在运行的容器。

### 构建镜像(dockerfile)

```bash
docker build -t wp:v1.0 .
```

docker build -t 自定义镜像名称：版本  “Dockerfile 文件存放目录的路径”

```bash
# Dockerfile 文件示例
cat > Dockerfile << EOF
FROM nginx:latest

LABEL version="1.0" maintainer="docker user <docker_user@github>"

RUN echo Testing Dockerfile ... > /usr/share/nginx/html/index.html
EOF
```

### 导出容器

```bash
docker export 2f41f4b4bed1 > nginx-test-images.tar
```

docker export “容器 ID 或 容器名称” > 镜像包名称

### 导入容器

```bash
docker import - new-nginx-test:v1.1 < nginx-test-images.tar
```

docker import - 自定义镜像名称：版本 < 镜像包

### 创建镜像(根据容器)

```bash
docker commit -m "testing-001" -a "Alan" 9d0d402af82a test-001:v1.0
```

docker commit -m "提交信息" -a "作者" "容器 ID" "自定义镜像名称：版本"

-p ：提交时暂停容器运行。

-c ：提交的时候执行 Dockerfile 指令，包括 CMD ENTRYPOINT ENV  EXPOSE LABEL ONBUILD USER VOLUME WORKDIR 等；

### 保存镜像（生成镜像包文件）

```bash
# 将一个镜像保存为一个镜像包文件
docker save 590c8328bda3 > save-images-1.tar

# 将多个镜像保存为一个镜像包文件
docker save -o save-images-2.tar tomcat:latest new-nginx-test:v1.1
```

docker save ”镜像 ID 或 镜像名称：版本“ > 镜像包文件名称

docker save -o 镜像包文件名称 ”镜像 ID 或 镜像名称：版本“ ”镜像 ID 或 镜像名称：版本“ ...

### 载入镜像（导入镜像包文件）

```bash
docker load < save-images-1.tar
```

docker load < 镜像包文件

### 标记镜像

```bash
docker tag wp:v1.0 10.0.2.3:5000/registry-images-test:v1.1
```

docker tag ”源容器名称：版本“ ”标记后的容器名称：版本“



# 搭建 Docker 私有仓库

## 1. 搭建私有 Docker Registry

### 方法一：使用 YUM 安装 docker-distribution

```bash
yum install docker-distribution -y
systemctl start docker-distribution && systemctl enable docker-distribution
```

### 方式二：使用容器运行

```bash
docker run --restart always --name registry -p 5000:5000 -v /var/lib/registry:/var/lib/registry -d registry
```

### 解决无法上传下载镜像（默认只支持 https 协议）的问题

```bash
# 修改 docker daemon 文件并重启服务
cat > /etc/docker/daemon.json << EOF
{
   "registry-mirrors": ["<https://mirror.ccs.tencentyun.com>"],
   "insecure-registries": ["10.0.2.3:5000"]
}
EOF

systemctl restart docker
```

加入 "insecure-registries": ["10.0.2.3:5000"] 这项参数，可以理解为将此私有仓库认证为安全仓库。其他客户端需要上传镜像或者从此仓库下载镜像，也需要修改该客户端本地的 docker daemon 文件加入此参数并重启 docker 服务。

### 上传镜像

```bash
# 需将要上传的镜像标记为指定的名称前缀，例如上传 tomcat 镜像
docker tag busybox:latest 10.0.2.3:5000/busybox:v1.0

# 上传指定镜像到私有仓库
docker push 10.0.2.3:5000/busybox:v1.0
```

### 下载镜像

```bash
# 先删除原有的镜像，确保本地没有此镜像
docker rmi 10.0.2.3:5000/busybox:v1.0

# 从私有仓库拉取指定的镜像
docker pull 10.0.2.3:5000/busybox:v1.0
```

### 查看目前有哪些镜像

```bash
ls /var/lib/registry/docker/registry/v2/repositories
```

## 2. 搭建私有仓库 Harbor

待完善

# 数据卷管理

数据卷 (Data Volumes) 是一个可供容器使用的特殊目录，它将主机操作系统目录直接映射进容器，类似于 Linux 中的 mount 行为。

- 数据卷可以在容器之间共享和重用，容器间传递数据变得高效与方便；
- 对数据卷内数据的修改会立刻生效，无论是容器内操作还是本地操作；
- 对数据卷的更新不影响镜像，解耦开应用和数据；
- 卷会一直存在，直到没有容器使用，可以安全地卸载它。

## 创建数据卷

```bash
docker volume create -d local test
```

create ：Create a volume inspect ：Display detailed information on one or more volumes ls ：List volumes prune ：Remove all unused local volumes rm ：Remove one or more volumes

数据卷所在目录：/var/lib/docker/volumes/

## 绑定数据卷

除了使用 volume 子命令来管理数据卷外，还可以在创建容器时将主机本地的任意路径挂载到容器内作为数据卷。

运行容器时可以使用 -mount 选项来使用数据卷，-mount 支持三种类型的数据卷：

- volume ：普通数据卷，映射到 /var/lib/docker/volumes/ 目录下；
- bind ：绑定数据卷，映射到主机指定路径下；
- tmpfs ：临时数据卷，只存在于内存中。

```bash
docker run -d --name web --mount type=bind,source=/www,destination=/usr/share/nginx/html nginx:latest
# 等同于
docker run -d --name web -v /www:/usr/share/nginx/html nginx:latest
```

Docker 挂载数据卷的默认权限为可读可写(rw)，用户也可以通过 ro 指定为只读：

```bash
docker run -d --name web -v /www:/usr/share/nginx/html:ro nginx:latest
```

加上 :ro 后，在容器内就无法修改 挂载数据卷内 的数据了。

## 数据卷容器

如果用户需要在多个容器之间共享一些持续更新的数据，那么最简单的方式就是使用数据卷容器。数据卷容器也是一个容器，它的目的是专门提供数据卷给其他的容器挂载。

```bash
docker run -it -v /data --name data centos

docker run -it --volumes-from data --name db01 centos

docker run -it --volumes-from data --name db02 centos
```

db01 和 db02 都挂载同一个数据卷到相同的 /data 目录。

使用 —volumes-from 参数所挂载数据卷的容器自身并不需要保持运行状态。

删除 data db01 db02 任意挂载的容器，数据卷并不会被删除。如果要将数据卷删除，必须在删除最后一个还挂载着它的容器时使用 docker rm -v 命令来指定同时删除与容器关联的数据卷。

## 利用数据卷容器来迁移数据

### 备份

```bash
# 备份 data 数据卷容器内的数据卷
docker run --volumes-from data -v $(pwd):/backup --name worker centos tar zcvf /backup/bak.tar /data
```

1. 利用 centos 镜像创建一个容器 worker；
2. 使用 —volumes-from data 参数让 worker 容器挂载 data 容器的数据卷；
3. 使用 -v $(pwd):/backup 参数挂载本地当前目录到 worker 容器的 /backup 目录
4. worker 容器启动后，使用 tar zcvf /backup/bak.tar /data 命令将 /data 下的内容备份到 worker 容器的 /backup/bak.tar ，即宿主机的当前目录下。

### 恢复

```bash
docker run --volumes-from data -v $(pwd):/backup centos tar xvf /backup/bak.tar -C /data
```



# 端口映射与容器互联

## 1. 端口映射实现容器访问

### 随机映射端口到内部容器开放的网络端口

```bash
docker run -d -P nginx:latest
```

0.0.0.0:32770->80/tcp

-P ：随机映射一个本机端口到 nginx 容器的 80 端口

### 映射到指定端口

```bash
docker run -d -p 2080:80 nginx:latest
docker run -d -p 2001:80 -p 2002:443 nginx:latest
```

0.0.0.0:2080->80/tcp

-p ：映射本地的 2080 端口到 nginx 容器的 80 端口

0.0.0.0:2001->80/tcp, 0.0.0.0:2002->443/tcp

-p ：分别映射本地的 2001 和 2002 端口到 nginx 容器的 80 和 443 端口

### 映射到指定地址的指定端口

```bash
docker run -d -p 127.0.0.1:2003:80 nginx:latest
```

127.0.0.1:2003->80/tcp

-p ：映射 127.0.0.1 地址的 2003 端口到 nginx 容器的 80 端口

### 映射到指定地址的随机端口

```bash
docker run -d -p 127.0.0.1::80 nginx:latest
```

127.0.0.1:32768->80/tcp

### 映射到指定地址的指定 udp 端口

```bash
docker run -d -p 127.0.0.1:2004:80/udp nginx:latest
```

80/tcp, 127.0.0.1:2004->80/udp

## 2. 容器互联

—link 参数可以让容器之间安全地进行交互。

```bash
# 先创建一个数据库容器
docker run -d --name db -e MYSQL_ROOT_PASSWORD=linux mysql:5.7

# 再创建一个 web 容器
docker run -d -p 1080:80 --name web --link db:db nginx:latest
```

—link 参数格式为 —link name:alias ，其中 name 是要链接的容器名称，alias 是别名。

Docker 相当于在两个容器之间创建了一个通道，而不直接映射他们的端口到宿主机上。启动 db 容器的时候并没有使用 -p 和 -P 标记，从而避免了暴露数据库服务端口到外部网络。

Docker 通过两种方式为容器公开连接信息：

- 更新环境变量
- 更新 /etc/hosts 文件

```bash
docker exec -it web sh
# env
DB_PORT_33060_TCP_ADDR=172.17.0.2
HOSTNAME=bf5ac571974b
DB_PORT=tcp://172.17.0.2:3306
DB_PORT_3306_TCP=tcp://172.17.0.2:3306
DB_PORT_33060_TCP_PORT=33060
DB_ENV_MYSQL_VERSION=5.7.30-1debian10
DB_PORT_33060_TCP_PROTO=tcp
HOME=/root
DB_NAME=/web/db
PKG_RELEASE=1~buster
DB_PORT_33060_TCP=tcp://172.17.0.2:33060
TERM=xterm
NGINX_VERSION=1.17.10
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
NJS_VERSION=0.3.9
DB_ENV_MYSQL_MAJOR=5.7
DB_PORT_3306_TCP_ADDR=172.17.0.2
DB_ENV_MYSQL_ROOT_PASSWORD=linux
PWD=/
DB_ENV_GOSU_VERSION=1.12
DB_PORT_3306_TCP_PORT=3306
DB_PORT_3306_TCP_PROTO=tcp

# cat /etc/hosts
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
172.17.0.2	db 567c62f44e3d
172.17.0.3	bf5ac571974b
```



# 使用 Dockerfile 创建镜像

Dockerfile 由一行一行的命令语句组成，并且支持以 # 开头的注释行。

一般而言，Dockerfile 主体内容分为四个部分：基础镜像信息、维护者信息、镜像操作指令和容器启动时的执行指令。

[Dockerfile reference](https://docs.docker.com/engine/reference/builder/)

## 指令说明

### 1. ARG 定义创建镜像过程中使用的变量

ARG <name>[=<default value>]

在执行 docker build 时，可以通过 --build-arg <varname>=<value> 来为变量赋值。当镜像编译成功后，ARG 指定的变量将不再存在。

Docker 内置了一些预定义的 ARG 变量，用户可以直接使用，无须在 Dockerfile 中声明。

- `HTTP_PROXY`
- `http_proxy`
- `HTTPS_PROXY`
- `https_proxy`
- `FTP_PROXY`
- `ftp_proxy`
- `NO_PROXY`
- `no_proxy`

### 2. FROM 指定创建镜像的基础镜像

FROM [--platform=<platform>] <image> [AS <name>]

FROM [--platform=<platform>] <image>[:<tag>] [AS <name>]

FROM [--platform=<platform>] <image>[@<digest>] [AS <name>]

Dockerfile 中的第一条指令必须是 FROM 指令（ARG 除外）。如果在同一个 Dockerfile 中创建多个镜像时，可以使用多个 FROM 指令。

```bash
ARG VERSION=latest
FROM centos:${VERSION}
```

### 3. LABEL 为生成的镜像添加元数据标签信息

LABEL <key>=<value> <key>=<value> <key>=<value> ...

```bash
LABEL version="1.0.0"
LABEL author="alan@yahoo" date="2020-05-20"
LABEL description="This text illustrates \\
that label-values can span multiple lines."
```

### 4. EXPOSE 声明容器在运行时服务监听的端口

EXPOSE <port> [<port>/<protocol>...]

默认情况下，EXPOSE 假定协议为 TCP

```bash
EXPOSE 80 443
EXPOSE 80/udp
```

该指令只是起到声明的作用，并不会自动完成端口映射。在启动容器时可以使用 -P 参数完成映射。

### 5. ENV 指定环境变量

在构建阶段中所有后续指令的环境中使用，在镜像启动的容器中也会存在。

ENV <key> <value> 或者 ENV <key>=<value> ...

```bash
ENV myName="John Doe" myDog=Rex\\ The\\ Dog \\
    myCat=fluffy
ENV myName John Doe
ENV myDog Rex The Dog
```

指令指定的环境变量在容器运行时可以被覆盖掉，如 docker run -e myName=Alan nginx:latest 。

使用 ENV 指令定义的环境变量，始终会覆盖 ARG 同名的变量。

当一条 ENV 指令中同时为多个环境变量赋值，并且值也是从环境变量中读取时，会为变量都赋值后再更新。如下指令，最终结果为 key1=value1 key2=value2 。

```bash
ENV key1=value2
ENV key1=value1 key2=${key1}
```

### 6. ENTRYPOINT 指定镜像的默认入口命令

该入口命令会在容器启动时作为根命令执行（pid 1 号进程），所有传入值作为该命令的参数。

支持两种格式：

ENTRYPOINT ["executable", "param1", "param2"]  ：exec 调用执行

ENTRYPOINT command param1 param2  ：shell 中执行

CMD 指令指定值将作为根命令的参数。

每个 Dockerfile 中只能有一个 ENTRYPOINT，当指定多个时，只有最后一个生效。

在启动容器时，可以被 —entrypoint 参数覆盖掉。

```bash
FROM ubuntu
ENTRYPOINT ["top", "-b"]
CMD ["-c"]
```

### 7. VOLUME 创建一个数据卷挂载点

VOLUME /data 或者 VOLUME ["/data"]

运行容器时可以从本地主机或其它容器挂载数据卷，一般用于存放数据库和需要保持的数据等。

```bash
FROM ubuntu
RUN mkdir /myvol
RUN echo "hello world" > /myvol/greeting
VOLUME /myvol
```

### 8. USER 指定构建镜像和运行容器时的用户和可选的用户组

USER <user>[:<group>] 或者 USER <UID>[:<GID>]

```bash
FROM microsoft/windowsservercore
# Create Windows user in the container
RUN net user /add patrick
# Set it for subsequent commands
USER patrick
```

docker run 运行容器时，可以通过 -u 参数来覆盖所指定的用户。

### 9. WORKDIR 指定工作目录

WORKDIR /path/to/workdir

Dockerfile 中其后的命令 RUN、CMD、ENTRYPOINT、ADD、COPY 等命令都会在该目录下执行。

该 WORKDIR 指令可以在中多次使用Dockerfile。如果提供了相对路径，则它将相对于上一条 WORKDIR 指令的路径 。例如：

```bash
WORKDIR /a
WORKDIR b
WORKDIR c
RUN pwd
```

最终的输出 pwd 命令结果将是 /a/b/c 。

使用 docker run 运行容器时，可以通过 -w 参数覆盖构建时所设置的工作目录。

### 10. ONBUILD 当该镜像用作另一个构建的基础镜像时，将会自动执行的操作指令

ONBUILD <INSTRUCTION>

当该镜像被作为 Dockerfile 中的基础镜像的时候，使用 docker build 构建镜像时，会首先执行基础镜像中配置的 ONBUILD 指令。

```bash
FROM centos
ONBUILD RUN mkdir /onbuild-testing
# 使用上述 Dockerfile 构建镜像后（镜像命名为 c1:latest），基于该镜像启动容器后并不会发现 /onbuild-testing 目录。

FROM c1:latest
RUN echo "hello world" > /m
# 使用上述 Dockerfile 构建镜像后，发现 /onbuild-testing 目录存在。
```

### 11. STOPSIGNAL 指定容器退出时所要被发送的系统调用信号

STOPSIGNAL signal

所使用的信号(signal)必须是内核系统调用表中的合法的值，如 SIGKILL 或 9 。

```bash
# kill -l
 1) SIGHUP	 2) SIGINT	 3) SIGQUIT	 4) SIGILL	 5) SIGTRAP
 6) SIGABRT	 7) SIGBUS	 8) SIGFPE	 9) SIGKILL	10) SIGUSR1
11) SIGSEGV	12) SIGUSR2	13) SIGPIPE	14) SIGALRM	15) SIGTERM
16) SIGSTKFLT	17) SIGCHLD	18) SIGCONT	19) SIGSTOP	20) SIGTSTP
21) SIGTTIN	22) SIGTTOU	23) SIGURG	24) SIGXCPU	25) SIGXFSZ
26) SIGVTALRM	27) SIGPROF	28) SIGWINCH	29) SIGIO	30) SIGPWR
31) SIGSYS	34) SIGRTMIN	35) SIGRTMIN+1	36) SIGRTMIN+2	37) SIGRTMIN+3
38) SIGRTMIN+4	39) SIGRTMIN+5	40) SIGRTMIN+6	41) SIGRTMIN+7	42) SIGRTMIN+8
43) SIGRTMIN+9	44) SIGRTMIN+10	45) SIGRTMIN+11	46) SIGRTMIN+12	47) SIGRTMIN+13
48) SIGRTMIN+14	49) SIGRTMIN+15	50) SIGRTMAX-14	51) SIGRTMAX-13	52) SIGRTMAX-12
53) SIGRTMAX-11	54) SIGRTMAX-10	55) SIGRTMAX-9	56) SIGRTMAX-8	57) SIGRTMAX-7
58) SIGRTMAX-6	59) SIGRTMAX-5	60) SIGRTMAX-4	61) SIGRTMAX-3	62) SIGRTMAX-2
63) SIGRTMAX-1	64) SIGRTMAX
```

### 12. HEALTHCHECK 配置如何测试容器以检查其是否仍在工作

该 HEALTHCHECK 指令有两种形式：

- HEALTHCHECK [OPTIONS] CMD command （通过在容器内部运行命令来检查容器的运行状况）
- HEALTHCHECK NONE （禁用从基础镜像中继承的任何运行状况检查）

OPYIONS 支持如下参数：

- `--interval=DURATION`（默认值：`30s`）每次检查后的间隔数秒
- `--timeout=DURATION`（默认值：`30s`）每次检查等待结果的超时时间
- `--start-period=DURATION`（默认值：`0s`）开始时间段为需要时间进行引导的容器提供了初始化时间。在此期间内的探针故障将不计入最大重试次数。但是，如果运行状况检查在启动期间成功，则认为该容器已启动，并且所有连续失败将计入最大重试次数。
- `--retries=N`（默认值：`3`）如果检测失败，需要重试几次才最终判定为失败

### 13. SHELL 设置执行命令时所使用的默认 shell 类型

SHELL ["executable", "parameters"]

Linux 上，默认值为 ["/bin/sh", "-c"] 。

该 SHELL 指令可以出现多次。每个 SHELL 指令将覆盖所有先前的 SHELL 指令，并影响所有后续的指令。例如：

```bash
FROM microsoft/windowsservercore

# Executed as cmd /S /C echo default
RUN echo default

# Executed as cmd /S /C powershell -command Write-Host default
RUN powershell -command Write-Host default

# Executed as powershell -command Write-Host hello
SHELL ["powershell", "-command"]
RUN Write-Host hello

# Executed as cmd /S /C echo hello
SHELL ["cmd", "/S", "/C"]
RUN echo hello
```

### 14. RUN 运行指定命令

RUN <command> 或者 RUN ["executable", "param1", "param2"]

后者指令会被解析为 JSON 数组，因此必须使用双引号。

前者默认在 shell 终端中运行命令，即 /bin/sh -c ；后者则使用 exec 执行，不会启动 shell 环境。

指定使用其他终端类型也可以通过第二种方式实现，例如 RUN ["/bin/bash","-c","echo hello"] 。

每条 RUN 指令会在当前镜像基础上执行指定命令，并提交为新的镜像层。当命令较长时可以使用反斜杠 \ 换行。例如：

```bash
RUN /bin/bash -c 'source $HOME/.bashrc; \\
echo $HOME'
```

### 15. CMD 指定容器启动时默认执行的命令

该 CMD 指令具有三种形式：

- `CMD ["executable","param1","param2"]`（*exec* 形式，推荐形式）
- `CMD ["param1","param2"]`（作为 *ENTRYPOINT 的默认参数*）
- `CMD command param1 param2`（在默认的 shell 中执行）

每个 Dockerfile 中只能有一条 CMD 指令。如果指定了多条，则只有最后一条会被执行。

如果在启动容器时手动指定了运行的命令（作为 run 命令的参数），则会覆盖掉 CMD 指定的命令。

### 16. ADD 添加内容到镜像

ADD [--chown=<user>:<group>] <src>... <dest>

ADD [--chown=<user>:<group>] ["<src>",... "<dest>"]

该命令将复制指定的 <src> 路径下的内容到镜像中的 <dest> 路径下。目标路径不存在时，会自动创建。

其中 <src> 可以是 Dockerfile 所在目录的一个相对路径（文件或目录）；也可以是一个 URL；还可以是一个 tar 文件（将自动解压为目录）。<dest> 可以是镜像内的绝对路径，也可以是相对于工作目录（WORKDIR）的相对路径。

<src> 路径支持通配符，如 ADD hom* /mydir/ 。

### 17. COPY 复制内容到镜像

COPY [--chown=<user>:<group>] <src>... <dest> COPY [--chown=<user>:<group>] ["<src>",... "<dest>"]

复制本地主机的 <src> 路径下的内容到镜像中的 <dest> 。目标路径不存在时，会自动创建。<src> 路径支持通配符。

- 该`<src>`路径必须在 Dockerfile 当前目录内; 不能跳出 Dockerfile 目录。例如：`COPY ../something /something` 将不能成功执行构建。
- 如果`<src>`是目录，则将复制目录的整个内容，包括文件系统元数据。目录本身不被复制，仅其内容被复制。
- 如果`<src>`是任何其他类型的文件，则将其及其元数据一起单独复制。在这种情况下，如果`<dest>`以斜杠结尾`/`，则将其视为目录，并将其内容`<src>`写入`<dest>/base(<src>)`。
- 如果`<src>`直接或由于使用通配符而指定了多个资源，则该资源`<dest>`必须是目录，并且必须以斜杠结尾`/`。
- 如果`<dest>`不以斜杠结尾，则将其视为常规文件，并将其内容`<src>`写入`<dest>`。
- 如果`<dest>`不存在，它将与路径中所有缺少的目录一起创建。

## 创建镜像

可以通过 docker build 命令来创建镜像。

docker build [OPTIONS] PATH | URL | -

该命令将读取指定路径下的 Dockerfile，并将该路径下所有的数据作为上下文（Context）发送给 Docker 服务端。Docker 服务端校验 Dockerfile 格式通过后，逐条执行其中定义的指令，碰到 ADD、COPY 和 RUN 指令会生成一层镜像。最终如果创建镜像成功，会返回最终的镜像 ID 。

可以通过 .dockerignore 文件（每一行添加一条匹配模式）来让 Docker 忽略匹配路径或文件，在创建镜像时不将无关的数据发送到服务端。例如：

```bash
cat > .dockerignore << EOF
*/temp*
*/*/temp*
tmp?
~*
Dockerfile
!README.md
EOF
```

- dockerignore 文件中模式语法支持 Golang 风格的路径正则格式
- - 表示任意多个字符
- ？ 表示单个任意字符
- ！表示不匹配（即不忽略指定的路径或文件）

### 多步骤构建

多步骤构建主要是为了解决一些在容器中进行编译工作，然后运行时容器只需要编译的结果，不需要整个编译环境被依赖，减少镜像的大小。

示例：

```bash
FROM golang:1.9 as builder # define stage name as builder
RUN mkdir -p /go/src/test
WORKDIR /go/src/test
COPY main.go
RUN CGO_ENABLED=O GOOS=linux go build -o app

FROM alpine:latest
RUN apk –no-cache add ca-certificates
WORKDIR /root/
COPY –from=builder /go/src/test/app . # copy file from the builder stage
CMD ["./app"]
```

### **Dockerfile 最佳实践**

- 精简镜像用途
- 选择较小且合适的基础镜像（指定版本，禁用 latest）
- 提供注释和维护者信息
- 规范使用版本号
- 减少镜像层数
- 恰当使用多步骤构建
- 使用 .dockerignore 文件忽略不必要的数据内容
- 清空临时文件和缓存文件
- 调整合理的指令顺序（在开启 cache 的情况下，内容不变的指令尽量放在前面）
- 减少外部源干扰（如果要从外部引入数据，请指定持久的地址，并带版本信息等）

# 系统镜像添加 SSH 服务

## Ubuntu

### 1. 创建 ubuntu_sshd 工作目录

```bash
mkdir ubuntu-sshd
cd $_
```

### 2. 配置 ssh 授权密钥文件

```bash
[ -f ~/.ssh/id_rsa ] || ssh-keygen -t rsa -f ~/.ssh/id_rsa -N ''
cat ~/.ssh/id_rsa.pub > authorized_keys
```

### 3. 编写 Dockerfile

```bash
cat > Dockerfile << 'EOF'
FROM ubuntu:latest

LABEL version="1.0.0" author="Alan" date="2020-05-22"

RUN /bin/bash -c "apt-get update; \\
apt-get install openssh-server -y; \\
mkdir -p /var/run/sshd; \\
sed -ri '/session    required/s%^%# %g' /etc/pam.d/sshd; \\
rm -rf /var/lib/apt/lists/*"

ADD authorized_keys /root/.ssh/

EXPOSE 22

CMD /usr/sbin/sshd -D
EOF
```

### 4. 创建镜像

```bash
docker build -t ubuntu-sshd:1.0.0 .
```

### 5. 运行容器

```bash
docker run -d -p 30022:22 ubuntu-sshd:1.0.0
```

### 6. 连接测试

```bash
ssh 127.0.0.1 -p 30022 -o StrictHostKeyChecking=no
```

## Centos

### 1. 创建 ubuntu_sshd 工作目录

```bash
mkdir centos-sshd
cd $_
```

### 2. 配置 ssh 授权密钥文件

```bash
[ -f ~/.ssh/id_rsa ] || ssh-keygen -t rsa -f ~/.ssh/id_rsa -N ''
cat ~/.ssh/id_rsa.pub > authorized_keys
```

### 3. 编写 Dockerfile

```bash
cat > Dockerfile << 'EOF'
FROM centos:latest

LABEL version="1.0.0" author="Alan" date="2020-05-22"

RUN /bin/bash -c 'yum install openssh-server -y; \\
ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key -N ""; \\
ssh-keygen -t ecdsa -f /etc/ssh/ssh_host_ecdsa_key -N ""; \\
ssh-keygen -t ed25519 -f /etc/ssh/ssh_host_ed25519_key -N ""; \\
yum clean all'

ADD authorized_keys /root/.ssh/

EXPOSE 22

CMD /usr/sbin/sshd -D
EOF
```

### 4. 创建镜像

```bash
docker build -t centos-sshd:1.0.0 .
```

### 5. 运行容器

```bash
docker run -d -p 20022:22 centos-sshd:1.0.0
```

### 6. 连接测试

```bash
ssh 127.0.0.1 -p 20022 -o StrictHostKeyChecking=no
```
