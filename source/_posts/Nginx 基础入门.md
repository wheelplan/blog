---
title: Nginx 基础入门
date: 2017-03-01 23:56:21
top_img: https://pic1.zhimg.com/v2-e68d524210343613129267bd2cb75a0d_1200x500.jpg
keywords: nginx 部署 反向代理 负载均衡 性能优化 yum 仓库
tags:
 - nginx

---

# Nginx

### Nginx 介绍

##### 1.Nginx 是什么 ？

Nginx 是一款轻量级 Web 服务器 / 反向代理服务器及电子邮件（IMAP/POP3）代理服务器，在 BSD-like 协议下发行。其特点是占有内存少，[并发](https://baike.baidu.com/item/并发/11024806)能力强，事实上 nginx 的并发能力在同类型的网页服务器中表现较好，中国大陆使用 nginx 网站用户有：百度、[京东](https://baike.baidu.com/item/京东/210931)、[新浪](https://baike.baidu.com/item/新浪/125692)、[网易](https://baike.baidu.com/item/网易/185754)、[腾讯](https://baike.baidu.com/item/腾讯/112204)、[淘宝](https://baike.baidu.com/item/淘宝/145661) 等。

Nginx 是一个开源且高性能、可靠的 Http Web 服务、代理服务。

开源: 直接获取源代码

高性能: 支持海量并发

可靠: 服务稳定

##### 2.我们为什么选择 Nginx 服务 ？

Nginx 非常轻量

功能模块少 (源代码仅保留 http 与核心模块代码,其余不够核心代码会作为插件来安装)

代码模块化 (易读，便于二次开发，对于开发人员非常友好)

互联网公司都选择 Nginx

1.Nginx 技术成熟，具备的功能是企业最常使用而且最需要的

2.适合当前主流架构趋势, 微服务、云架构、中间层

3.统一技术栈, 降低维护成本, 降低技术更新成本。

##### 3.Nginx 的重要特性

Nginx 采用 Epool 网络模型， Apache 采用 Select 模型

Select：当用户发起一次请求， select 模型就会进行一次遍历扫描，从而导致性能低下。

Epool：当用户发起请求， epool 模型会直接进行处理，效率高效，并无连接限制

<br>

### Nginx 安装部署

1.源码编译(1.版本随意 2.安装复杂 3.升级繁琐)

2.epel 仓库(1.版本较低 2.安装简单 3.配置不易读)

3.官方仓库(1.版本较新 2.安装简单 3.配置易读，推荐)

下面分别介绍编译安装和 yum 安装方法

#### ⦁	 编译安装方法

```bash
# 创建www用户
groupadd www -g 666
useradd www -s /sbin/nologin -M -u 666 -g 666

# 安装依赖包
yum install openssl-devel pcre-devel -y

# 下载解压软件包
mkdir /data/soft -p
cd /data/soft/
wget http://nginx.org/download/nginx-1.16.0.tar.gz
tar zxvf nginx-1.16.0.tar.gz 

# 配置编译参数
cd /data/soft/nginx-1.16.0/
./configure --help
./configure --user=www --group=www --prefix=/opt/nginx-1.16.0/ --with-http_stub_status_module --with-http_ssl_module --with-pcre

# 编译安装
make && make install

# 创建软链接
ln -s /opt/nginx-1.16.0/ /opt/nginx

# 检查语法
/opt/nginx/sbin/nginx -t

# 启动nginx
/opt/nginx/sbin/nginx

# 检查测试
netstat -lntup | grep nginx
curl 10.0.1.7
```

#### ⦁	 YUM 安装方法

```bash
# 安装依赖包
yum install openssl-devel pcre-devel -y

# 配置官方 yum 源
cat > /etc/yum.repos.d/nginx.repo << 'EOF'
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key

[nginx-mainline]
name=nginx mainline repo
baseurl=http://nginx.org/packages/mainline/centos/$releasever/$basearch/
gpgcheck=1
enabled=0
gpgkey=https://nginx.org/keys/nginx_signing.key
EOF

# 安装 nginx 服务
yum install nginx -y

# 启动服务并配置开机自启动
systemctl start nginx
systemctl enable nginx

# 测试访问 
curl 10.0.1.7
```

<br>

#### ⦁	 Nginx 启动方式

编译安装启动管理方式

```bash
nginx -t
nginx
nginx -s reload
nginx -s stop
```

yum 安装启动管理方法

```bash
nginx -t
systemctl start nginx
systemctl reload nginx
systemctl restart nginx
systemctl stop  nginx
```

### Nginx 重要配置文件说明

```bash
# 查看配置文件
rpm -ql nginx                      
/etc/logrotate.d/nginx                     # nginx 日志切割的配置文件
/etc/nginx/nginx.conf                      # nginx 主配置文件 
/etc/nginx/conf.d                          # 子配置文件
/etc/nginx/conf.d/default.conf             # 默认展示的页面一样 
/etc/nginx/mime.types                      # 媒体类型 （http 协议中的文件类型）
/etc/sysconfig/nginx                       # systemctl 管理 nginx 的使用的文件
/usr/lib/systemd/system/nginx.service      # systemctl 管理 nginx（开 关 重启 reload)配置文件       
/usr/sbin/nginx                            # nginx命令
/usr/share/nginx/html                      # 站点目录 网站的根目录 
/var/log/nginx                             # nginx 日志 access.log 访问日志
```

⦁	 查看已经编译的模块 `nginx -V`

⦁	 配置文件注解

Nginx 主配置文件 /etc/nginx/nginx.conf 是一个纯文本类型的文件，整个配置文件是以区块的形式组织的。一般，每个区块以一对大括号{}来表示开始与结束。

Nginx 主配置文件整体分为三块，分别是 

CoreModule(核心模块)

EventModule(事件驱动模块)

HttpCoreModule(http 内核模块)

⦁	 第一部分：配置文件主区域配置

```bash
user nginx;                # 定义运行 nginx 进程的用户
worker_processes  1;       # Nginx 运行的 work 进程数量(建议与 CPU 数量一致或 auto)

error_log  /var/log/nginx/error.log warn;  # nginx 错误日志
pid        /var/run/nginx.pid;             # nginx 运行 pid
```

⦁	 第二部分：配置文件事件区域

```bash
events {
    worker_connections  1024;  # 每个 worker 进程支持的最大连接数
}
```

⦁	 第三部分：配置 http 区域

```bash
http {
    include       /etc/nginx/mime.types;          # Nginx 支持的媒体类型库文件
    default_type  application/octet-stream;       # 默认的媒体类型 

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    
    access_log  /var/log/nginx/access.log  main;    # 访问日志保存路径
    
    sendfile        on;                             # 开启高效传输模式
    #tcp_nopush     on;                       
    keepalive_timeout  65;                          # 连接超时时间
    #gzip  on;                                      # 开启压缩
    include /etc/nginx/conf.d/*.conf;               # 包含子配置文件

}    
```

⦁	 第四部分：子配置文件内容

```bash
egrep -v "# | ^$" /etc/nginx/conf.d/default.conf     
server {
    listen       80;             # 指定监听端口
    server_name  localhost;      # 指定监听的域名
    location / {              
        root   /usr/share/nginx/html;     # 定义站点的目录
        index  index.html index.htm;      # 定义首页文件
    }
    error_page   500 502 503 504  /50x.html;   # 错误页面信息
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

http server location 扩展了解项

http{} 层下允许有多个 Server{} 层，一个 Server{} 层下又允许有多个 Location

http{} 标签主要用来解决用户的请求与响应。

server{} 标签主要用来响应具体的某一个网站。

location{} 标签主要用于匹配网站具体 URL 路径。



### Nginx 虚拟主机配置实践

#### ⦁	基于域名的虚拟主机

```bash
cat > /etc/nginx/nginx.conf  << 'EOF'
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    
    access_log  /var/log/nginx/access.log  main;
    
    sendfile        on;
    #tcp_nopush     on;
    
    keepalive_timeout  65;
    
    #gzip  on;
    
    #include /etc/nginx/conf.d/*.conf;
    server   {
        listen       80;
        server_name  www.rocc.com;
        location / {
            root   /usr/share/nginx/html/www;
            index  index.html index.htm;
        }
    }
    server   {
        listen       80;
        server_name  blog.rocc.com;
        location / {
            root   /usr/share/nginx/html/blog;
            index  index.html index.htm;
        }
    }
}
EOF
```

#### ⦁	 基于端口的虚拟主机

端口号优先级比域名要高

```bash
cat > /etc/nginx/nginx.conf << 'EOF' 
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    
    access_log  /var/log/nginx/access.log  main;
    
    sendfile        on;
    #tcp_nopush     on;
    
    keepalive_timeout  65;
    
    #gzip  on;
    
    #include /etc/nginx/conf.d/*.conf;
    server   {
        listen       81;
        server_name  www.rocc.com;
        location / {
            root   /usr/share/nginx/html/www;
            index  index.html index.htm;
        }
    }
    server   {
        listen       82;
        server_name  blog.rocc.com;
        location / {
            root   /usr/share/nginx/html/blog;
            index  index.html index.htm;
        }
    }
}
EOF
```

#### ⦁	 基于 IP 的虚拟主机

添加第二 IP

```bash
ip addr add 10.0.0.11/24 dev eth0
```

配置文件

```bash
cat > /etc/nginx/nginx.conf << 'EOF'
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    
    access_log  /var/log/nginx/access.log  main;
    
    sendfile        on;
    #tcp_nopush     on;
    
    keepalive_timeout  65;
    
    #gzip  on;
    
    #include /etc/nginx/conf.d/*.conf;
    server   {
        listen       10.0.1.7:81;
        server_name  www.rocc.com;
        location / {
            root   /usr/share/nginx/html/www;
            index  index.html index.htm;
        }
    }
    server   {
        listen       10.0.1.11:82;
        server_name  blog.rocc.com;
        location / {
            root   /usr/share/nginx/html/blog;
            index  index.html index.htm;
        }
    }
}
EOF
```

#### ⦁	 Nginx 虚拟主机配置优化

所有配置都写入一个配置文件维护起来比较麻烦，如果修改错了，影响所有的页面，所以我们应该拆分 nginx 的配置文件为各个子配置

⦁	 Nginx主配置文件

```bash
cat > /etc/nginx/nginx.conf << 'EOF'
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    
    access_log  /var/log/nginx/access.log  main;
    
    sendfile        on;
    #tcp_nopush     on;
    
    keepalive_timeout  65;
    
    #gzip  on;
    
    include /etc/nginx/conf.d/*.conf;

}
EOF
```



⦁	 子配置文件 www

```bash
cat > /etc/nginx/conf.d/01-www.conf << 'EOF'
server   {
    listen       80;
    server_name  www.rocc.com;
    location / {
        root   /usr/share/nginx/html/www;
        index  index.html index.htm;
    }
}
EOF
```



⦁	 子配置文件 blog

```bash
cat > /etc/nginx/conf.d/02-blog.conf << 'EOF'
server   {
    listen       80;
    server_name  blog.rocc.com;
    location / {
        root   /usr/share/nginx/html/blog;
        index  index.html index.htm;
    }
}
EOF
```

⦁	 创建代码目录及首页

```bash
mkdir /usr/share/nginx/html/{www,blog}
echo "www" > /usr/share/nginx/html/www/index.html
echo "blog" > /usr/share/nginx/html/blog/index.html 
```

⦁	 检查语法重启服务

```bash
nginx -t
# nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
# nginx: configuration file /etc/nginx/nginx.conf test is successful
systemctl restart nginx
```

⦁	 访问测试

```bash
tail -1 /etc/hosts 
# 10.0.1.7 www.rocc.com blog.rocc.com
curl www.rocc.com
# www
curl blog.rocc.com
# blog
```

### ⦁	 Nginx 状态模块

nginx状态模块：  --with-http_stub_status_module

⦁	状态模块配置文件

```bash
cat > status.conf << EOF
server {
   listen 80;
   server_name  status.rocc.com;
   stub_status on;
   access_log off;
}
EOF

tail -1 /etc/hosts   
# 10.0.1.7 www.rocc.com blog.rocc.com status.rocc.com

nginx -t
# nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
# nginx: configuration file /etc/nginx/nginx.conf test is successful

systemctl restart nginx
curl status.rocc.com 
# Active connections: 1 
# server accepts handled requests
#  1 1 1 
# Reading: 0 Writing: 1 Waiting: 0 
```

### Nginx 日志

⦁	 日志字段解释

```bash
$remote_addr # 记录客户端 IP 地址
$remote_user # 记录客户端用户名
$time_local # 记录通用的本地时间
$time_iso8601 # 记录 ISO8601 标准格式下的本地时间
$request # 记录请求的方法以及请求的 http 协议
$status # 记录请求状态码(用于定位错误信息)
$body_bytes_sent # 发送给客户端的资源字节数，不包括响应头的大小
$bytes_sent # 发送给客户端的总字节数
$msec # 日志写入时间。单位为秒，精度是毫秒。
$http_referer # 记录从哪个页面链接访问过来的
$http_user_agent # 记录客户端浏览器相关信息
$http_x_forwarded_for #记录客户端 IP 地址
$request_length # 请求的长度（包括请求行， 请求头和请求正文）。
$request_time # 请求花费的时间，单位为秒，精度毫秒

# 注:如果 Nginx 位于负载均衡器， nginx 反向代理之后， web 服务器无法直接获取到客 户端真实的 IP 地址。
# $remote_addr 获取的是反向代理的 IP 地址。 反向代理服务器在转发请求的 http 头信息中，
# 增加 X-Forwarded-For 信息，用来记录客户端 IP 地址和客户端请求的服务器地址。
```



### Nginx 防坑指南

1.端口优先级高于域名

2.ip + 端口的优先级是最高的

3.所有域名都匹配不上的时候,默认转发到根据 ASCII 码排序优先的配置文件

4.可以添加参数指定默认匹配的页面,这样就无需修改文件名了

```bash
server {
    listen       80 default_server;
    server_name  www.my.com;
    location / {
        root   /code/www;
        index  index.html index.htm;
    }
}
```

# 常用模块

## 1. Nginx 目录索引

### autoindex 语法

语法 Syntax ：autoindex on | off;

默认值 Default ：autoindex off;

环境 Context ：http, server, location

### autoindex 常用参数

**autoindex_exact_size off;**

默认为 on， 显示出文件的确切大小，单位是 bytes。

修改为 off，显示出文件的大概大小，单位是 kB 或者 MB 或者 GB。

**autoindex_localtime on;**

默认为 off，显示的文件时间为 GMT 时间。

修改为 on， 显示的文件时间为文件的服务器时间。

**charset utf-8,gbk;**

默认中文目录乱码，添加上解决乱码。

### 配置实例

```bash
server {
    listen       80;
    server_name  download.rocc.com;

    location / {
        root  /usr/share/nginx/html_download ;
        charset utf-8,gbk;
        autoindex on;
        autoindex_localtime on;
        autoindex_exact_size off;
}
}
```

## 2. Nginx 状态监控

### stub_status 语法（ngx_http_stub_status_module）

语法 Syntax ：stub_status;

环境 Context ：server, location

### 状态字段释义

Active connections # 当前活动的连接数

accepts	# 当前的总连接数 TCP

handled	# 成功的连接数 TCP

requests	# 总的 http 请求数

Reading	# 请求

Writing	# 响应

Waiting # 等待的请求数，开启了 keepalive

注意, 一次 TCP 的连接，可以发起多次 http 的请求, 如下参数可配置进行验证

keepalive_timeout 0;	# 类似于关闭长连接

keepalive_timeout 65;	# 65s 没有活动则断开连接

### 配置实例

```bash
server {
    listen       80;
    server_name  download.rocc.com;
		access_log off;

    location /status {
        stub_status;
}
}
```

## 3. Nginx 访问控制

### 基于 IP 的访问控制 ngx_http_access_module

**允许配置语法**

Syntax: allow address | CIDR | unix: | all;

Default: —

Context: http, server, location, limit_except

**拒绝配置语法**

Syntax: deny address | CIDR | unix: | all;

Default: —

Context: http, server, location, limit_except

**配置实例（只允许 3.3.3.1 访问，其他全部拒绝）**

```
location / {
……                  # 省略
    allow 3.3.3.1;
    deny all;
}
```

**配置实例（只拒绝 3.3.3.0/24 网段访问，其他全部允许访问）**

```
location / {
……                  # 省略
    deny 3.3.3.0/24;
    allow all;
}
```

### 基于用户登录认证的访问控制 ngx_http_auth_basic_module

**访问提示字符串语法**

Syntax: auth_basic string | off;

Default: auth_basic off;

Context: http, server, location, limit_except

**账户密码文件语法**

Syntax: auth_basic_user_file file;

Default: -

Context: http, server, location, limit_except

**配置实例**

需要安装 httpd-tools，该包中携带了 htpasswd 命令

```
yum install httpd-tools -y
```

创建新的密码文件, -c 创建新文件 -b 允许命令行输入密码

```
htpasswd -b -c /etc/nginx/auth_conf alan linux
```

配置默认网站配置文件

```
server {
    listen       80 default_server;
    server_name  rocc.com;

    location / {
        root   /usr/share/nginx/html ;
        index  index.html index.htm;
        auth_basic "Auth access WWW Input Your Password!";
        auth_basic_user_file auth_conf;
}
}
```

## Nginx 请求限制

语法 (ngx_http_limit_req_module)

Syntax: limit_req_zone key zone=name:size rate=rate;

Default: -

Context: http

引用限速模块

Syntax: limit_conn zone number [burst=number] [nodelay];

Default: -

Context: http, server, location

参数介绍

定义一条规则

limit_req_zone $binary_remote_addr zone=one:10m rate=1r/s;

limit_req_zone	# 引用限速模块

$binary_remote_addr	# 判定条件，每个请求的 IP

zone=alan:10m	# 定义一个 zone 名称

rate=1r/s;	# 限制速度，1 秒 1 次

引用一条限速规则

limit_req zone=two burst=5 nodelay;

limit_req	# 引用限速规则语法

zone=alan	# 引用哪一条规则

burst=5	# 令牌桶，允许排队的数量

nodelay;	# 如果不希望在请求被限制时延迟过多的请求，则应使用参数 nodelay

**配置实例**

```
limit_req_zone $binary_remote_addr zone=alan:10m rate=1r/s;
server {
    listen       80 default_server;
    server_name  rocc.com;
    limit_req zone=alan burst=3 nodelay;
access_log  /var/log/nginx/www.access.log  main;

    location / {
        root   /usr/share/nginx/html ;
        index  index.html index.htm;
}
}
```

## Nginx Location

Location 语法

Syntax:	location [ = | ~ | ~* | ^~ ] uri { ... }

location @name { ... }

Default:	—

Context:	server, location

Location 语法优先级排序

### 配置实例测试 Location 优先级

```bash
 server_name  rocc.com;
    location / {
        rewrite ^  weibo.com ;
    }
    location = / {
        rewrite ^  <https://weibo.com/?topnav=1&mod=logo> ;
    }

    location /documents/ {
        rewrite ^  <http://www.baidu.com> ;
    }
    
    location ^~ /q {
        rewrite ^  <https://www.qq.com/?fromdefault> ;
    }
    location ~* \\.(gif|jpg|jpeg)$ {
        rewrite ^  <https://fanyi.baidu.com/?aldtype=16047#auto/zh/> ;
    }
    location ~ \\.mp4$ {
        rewrite ^  <https://man.linuxde.net/> ;
    }
    location ~* /* {
        rewrite ^  <http://www.baidu.com> ;
    }
    access_log off;

}
```



# 反向代理

## 代理介绍

**现实生活中代理的场景:**

房东

订票平台

**正向代理与反向代理**

以访问 Google 为例，客户端连接到 VPN 相当于正向代理。

VPN 代理请求访问后端服务器并返回属于反向代理。

## Nginx 反向代理参数解释

```bash
proxy_set_header Host $http_host; 
# lb 服务器将用户访问网站的 HOST 信息传递后后端的 web 服务器

proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;    
# 将用户的真实 IP 传递给后端的 web 服务器

proxy_connect_timeout 30;  # 代理与后端服务器连接超时时间(代理连接超时)
proxy_send_timeout 60;     # 后端服务器数据回传给 nginx 代理超时时间
proxy_read_timeout 60;     # 代理等待后端服务器的响应时间

proxy_buffering on;               
# 把后端返回的内容先放到缓冲区当中，然后再返回给客户端,边收边传,不是全部接收完再传给客户端

proxy_buffer_size 32k;     # 设置 nginx 代理保存用户头信息的缓冲区大小
proxy_buffers 4 128k;      # proxy_buffers 缓冲区
```

## 优化代理配置文件

将配置写入新文件，调用时使用 include 引用即可

```bash
cat > /etc/nginx/proxy_params << 'EOF'
proxy_set_header Host $http_host;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_connect_timeout 30;
proxy_send_timeout 60;
proxy_read_timeout 60;
proxy_buffering on;
proxy_buffer_size 32k;
proxy_buffers 4 128k;
EOF
```

## Nginx 反向代理实践

lb01	        10.0.0.11

web01	10.0.0.21

需求：访问 lb01 的 80 端口代理到 web01 的 8080 端口

### web01 配置

```bash
cat > /etc/nginx/conf.d/web.conf << EOF
server {
    listen 8080;
#    server_name www.rocc.top;
    location / {
        root /code;
        index index.html;
    }
}
EOF

mkdir /code -p
echo "web01" > /code/index.html

nginx -t
systemctl restart nginx
```

### lb01 配置

```
cat > /etc/nginx/conf.d/proxy.conf << EOF
server {
    listen 80;
    server_name www.rocc.top;
    location / {
        proxy_pass <http://10.0.0.21:8080>;
        include proxy_params;
    }
}
EOF

nginx -t 
systemctl restart nginx

echo '10.0.0.11 www.rocc.top' >> /etc/hosts
```

### 测试访问

```bash
curl www.rocc.top
```



# 负载均衡

## 为什么需要负载均衡

我们的 Web 服务器直接面向用户，往往要承载大量并发请求，单台服务器难以负荷。

我使用多台 WEB 服务器组成集群，前端使用 Nginx 负载均衡，将请求分散的打到我们的后端服务器集群中实现负载的分发。

那么会大大提升系统的吞吐率、请求性能、高容灾。

## 负载均衡和反向代理的关系

因为反向代理多台机器，所以可以达到负载均衡的效果

## 负载均衡配置场景

### 四层负载均衡

所谓四层负载均衡指的是 OSI 七层模型中的传输层。

Nginx 已经能支持 TCP/IP 的控制，所以只需要对客户端的请求进行 TCP/IP 协议的包转发就可以实现负载均衡。

它的好处是性能非常快、只需要底层进行应用处理，而不需要进行一些复杂的逻辑。

### 七层负载均衡

七层负载均衡它是在应用层，那么它可以完成很多应用方面的协议请求。

比如我们说的 http 应用的负载均衡，它可以实现 http 信息的改写、头信息的改写、安全应用规则控制、URL 匹配规则控制、以及转发,rewrite 等等的规则。

所以在应用层的服务里面，我们可以做的内容就更多，那么 Nginx 则是一个典型的七层负载均衡。

### 四层与七层负载均衡对比

四层负载均衡数据包在底层就进行了分发，而七层负载均衡数据包则是在最顶层进行分发

由此可以看出，七层负载均衡效率没有四负载均衡高。

但七层负载均衡更贴近于服务。

如:http 协议就是七层协议，我们可以用 Nginx 可以作会话保持，URL 路径规则匹配、head 头改写等等

这些是四层负载均衡无法实现的。

## Nginx 负载均衡实践

### 项目需求

访问 bbs.rocc.top 跳转到 172.16.1.7

访问 [www.rocc.top](https://www.notion.so/www.rocc.top) 跳转到 172.16.1.8

### 主机目录规划

lb01	        10.0.0.5

web01	172.16.1.7

web02	172.16.1.8

### Nginx 配置文件

```bash
cat > /etc/nginx/conf.d/www.conf << EOF
server {
    server_name www.rocc.top;
    listen 80;
    root /code/;
    index www.html;
}
EOF

cat > /etc/nginx/conf.d/bbs.conf << EOF
server {
    server_name bbs.rocc.top;
    listen 80;
    root /code/;
    index bbs.html;
}
EOF
```

### 生成测试页面

```bash
echo "$(hostname) bbs" > /code/bbs.html
echo "$(hostname) www" > /code/www.html
```

### Web 服务器测试访问

```bash
nginx -t
systemctl restart nginx
curl -H 'Host:www.rocc.top' 127.0.0.1
curl -H 'Host:bbs.rocc.top' 127.0.0.1
```

### lb 服务器 nginx 配置

```bash
cat > /etc/nginx/conf.d/proxy.conf << EOF
upstream bbs_pools{
        server 172.16.1.7;
    }

upstream www_pools{
        server 172.16.1.8;
    }

server {
    listen 80;
    server_name www.rocc.top;
    location / {
        proxy_pass http://www_pools;
        include proxy_params;
    }
}

server {
    listen 80;
    server_name bbs.rocc.top;
    location / {
        proxy_pass http://bbs_pools;
        include proxy_params;
    }
}
EOF
```

### 访问测试

```bash
nginx -t 
systemctl restart nginx
curl -H 'Host:www.rocc.top' 10.0.0.5
curl -H 'Host:bbs.rocc.top' 10.0.0.5
```

## 负载均衡调度算法实验

### Weight 实验

```bash
upstream www_pools {
   server 172.16.1.7 weight=1;
   server 172.16.1.8 weight=2;
}
```

### ip_hash 实验

```bash
upstream www_pools {
   ip_hash;
   server 172.16.1.7 ;
   server 172.16.1.8 ;
}
```

### url_hash 实验

```bash
upstream www_pools {
   hash   $request_uri;  
   server 172.16.1.7 ;
   server 172.16.1.8 ;
}
```

### 测试命令

```bash
for i in {1..100};do curl -s -H "host:www.rocc.top" 127.0.0.1;done | grep web02 | wc -l
```

## 负载均衡配置参数实验

### Down 参数

```bash
upstream www_pools {
   server 172.16.1.7 down;
}
```

### Backup 参数

```bash
 upstream www_pools {
   server 172.16.1.7 down;
   server 172.16.1.8 backup;
}
```

## 根据条件转发实验

### 根据客户端类型转发

需求：

如果用户是 iphone 就跳转到 iphone 页面

如果用户是安卓就跳转到安卓页面

如果用户是 pc 就跳转到 pc 页面

如果用户是 IE 就返回 403

**Web 服务器 nginx 配置**

```bash
cat > /etc/nginx/conf.d/sj.conf << EOF
server {
    listen 8080;
    server_name sj.rocc.top;
    location / {
        root /code/android;
        index index.html;
    }
}
server {
    listen 8081;
    server_name sj.rocc.top;
    location / {
        root /code/iphone;
        index index.html;
    }
}
server {
    listen 8082;
    server_name sj.rocc.top;
    location / {
        root /code/pc;
        index index.html;
    }
}
EOF
```

**生成测试页面**

```bash
mkdir -p /code/{android,iphone,pc}
echo "$(hostname) PC" > /code/pc/index.html
echo "$(hostname) Iphone" > /code/iphone/index.html
echo "$(hostname) Android" > /code/android/index.html
nginx -t
systemctl restart nginx
```

**lb 服务器配置 nginx**

```bash
cat > /etc/nginx/conf.d/sj.conf << 'EOF'
upstream android {
    server 172.16.1.8:8080;
}
upstream iphone {
    server 172.16.1.7:8081;
}
upstream pc {
    server 172.16.1.7:8082;
    server 172.16.1.8:8082;
}

server {
    listen 80;
    server_name sj.rocc.top;
    location / {
        #默认跳转至 pc 站点
        proxy_pass <http://pc>;
        include proxy_params;

        #如果客户端是 Iphone 则跳转到 iphone 的资源池
        if ($http_user_agent ~* "Iphone") {
            proxy_pass <http://iphone>;
        } 
    
        #如果客户端是 Android 则跳转到 android 的资源池
        if ($http_user_agent ~* "Android"){
            proxy_pass <http://android>;
        } 
    
        #如果客户端是 IE 浏览器，则返回 403 错误。
        if ($http_user_agent ~* "msie"){
            return 403;
        }
    }

}
EOF
```

**检查并重启 nginx**

```bash
nginx -t
systemctl restart nginx 
```

**测试访问**

```bash
echo "10.0.0.5 sj.oldboy.com" >> /etc/hosts 
curl sj.oldboy.com
curl -A "iphone" sj.rocc.top      
curl -A "android" sj.rocc.top 
curl -A "msie" sj.rocc.top 
```

### 根据文件类型转发

需求

访问图片格式就跳转到web01

访问其他地址就跳转到web02

**Web 服务器配置**

```bash
cat > /etc/nginx/conf.d/www.conf << EOF
server {
    listen 80;
    server_name www.rocc.top;
    location / {
        root /code;
        index index.html;
    }
}
EOF
```

**生成测试页面**

```bash
echo "$(hostname) www" > /code/index.html
cd /code/ && wget -O sun.jpg <http://pic.51yuansu.com/pic3/cover/02/27/64/59c008e1c7954_610.jpg> 
```

**lb 服务器 nginx 配置**

```bash
cat > /etc/nginx/conf.d/jpg.conf << EOF
upstream static {
    server 172.16.1.7;
}
upstream default {
    server 172.16.1.8;
}

server {
    listen 80;
    server_name www.rocc.top;
    location / {
            proxy_pass <http://default>;
             include proxy_params;
    }
    location ~ .*.(gif|jpg|jpeg|png|bmp|swf|css|js)$ {
            proxy_pass <http://static>;
            include proxy_params;
    }
}
EOF
```

**访问测试**

```bash
curl www.rocc.top
curl www.rocc.top/sun.jpg
```



# 性能优化

## 影响性能的主要因素

1、网络

- 网络的流量
- 网络是否丢包
- 这些会影响 http 的请求与调用

2、系统

- 硬件有没有磁盘损坏，磁盘速率
- 系统的负载、内存、系统稳定性

3、服务

- 连接优化、请求优化
- 根据业务形态做对应的服务设置

4、程序

- 接口性能
- 处理速度
- 程序执行效率

5、数据库

每个架构服务与服务之间都或多或少有一些关联，我们需要将整个架构进行分层，找到对应系统或服务的短板，然后进行优化。

## 系统性能优化

文件句柄（文件描述符），Linux 一切皆文件，文件句柄可以理解为就是一个索引。

- 文件句柄会随着我们进程的调用频繁增加
- 系统默认文件句柄是有限制的，不能让一个进程无限的调用
- 需要限制每个进程和每个服务使用多大的文件句柄
- 文件句柄是必须要调整的优化参数

设置方式：

- 系统全局性修改
- 用户局部性修改
- 进程局部性修改

```
配置文件 /etc/security/limits.conf

1、系统全局性修改
# * 代表所有用户
* soft nofile 25535
* hard nofile 25535

2.用户局部性修改
# 针对 root 用户，soft 仅提醒，hard 限制，nofile 打开最大文件数
root soft nofile 65535
root hard nofile 65535

3.进程局部性修改
# 针对 nginx 进程 （nginx 配置文件中设置）
worker_rlimit_nofile 100000

4.调整内核参数：让 time_wait 状态重用(端口重用)[flag]
vim /etc/sysctl.conf
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_timestamps = 1

# 查看添加的内核参数
sysctl -p

# 查看所有内核参数
sysctl -a
```

在高并发短连接的 TCP 服务器上，当服务器处理完请求后立刻主动正常关闭连接。这个场景下会出现大量 socket 处于 TIME_WAIT 状态。如果客户端的并发量持续很高，此时部分客户端就会显示连接不上。 我来解释下这个场景。主动正常关闭 TCP 连接，都会出现 TIMEWAIT。

为什么我们要关注这个高并发短连接呢？有两个方面需要注意：

1. 高并发可以让服务器在短时间范围内同时占用大量端口，而端口有个 `0 ~ 65535` 的范围，并不是很多，刨除系统和其他服务要用的，剩下的就更少了。
2. 在这个场景中，短连接表示业务处理 + 传输数据的时间远远小于 `TIMEWAIT` 超时的时间的连接

这里有个相对长短的概念，比如取一个 web 页面,`1` 秒钟的 `http` 短连接处理完业务，在关闭连接之后，这个业务用过的端口会停留在`TIMEWAIT` 状态几分钟，而这几分钟，其他`HTTP`请求来临的时候是无法占用此端口的。单用这个业务计算服务器的利用率会发现，服务器干正经事的时间和端口（资源）被挂着无法被使用的时间的比例是 1：几百，服务器资源严重浪费。（说个题外话，从这个意义出发来考虑服务器性能调优的话，长连接业务的服务就不需要考虑`TIMEWAIT`状态。同时，假如你对服务器业务场景非常熟悉，你会发现，在实际业务场景中，一般长连接对应的业务的并发量并不会很高。

## Nginx 性能优化

```bash
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

# cpu 亲和
# nginx 进程数，建议按照 cpu 的核心数来指定
worker_processes auto;
# 将不同的进程绑定到不同的 CPU 核心
worker_cpu_affinity auto;

# worker 进程的最大打开文件数
worker_rlimit_nofile 30000

events {
  # 网络 i/o 模型
  use epoll;
  
  # 调整 Nginx 单个进程允许的客户端最大连接数（max_clients = worker_processes * worker_connections）
  worker_connections 10000;
  
  # 默认情况下，Nginx 进程只会在一个时刻接收一个新的连接，我们可以配置 multi_accept 为 on，实现在一个时刻内可以接收多个新的连接，提高处理效率。该参数默认是 off，建议开启。
  multi_accept on;
  
}

# 
http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
    
# 指定使用 utf-8 字符集
    charset utf-8;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    log_format json '{ "time_local": "$time_local", '
                             '"remote_addr": "$remote_addr", '
                             '"referer": "$http_referer", '
                             '"request": "$request", '
                             '"status": $status, '
                             '"bytes": $body_bytes_sent, '
                             '"agent": "$http_user_agent", '
                             '"x_forwarded": "$http_x_forwarded_for", '
                             '"up_addr": "$upstream_addr",'
                             '"up_host": "$upstream_http_host",'
                             '"upstream_time": "$upstream_response_time",'
                             '"request_time": "$request_time"'
       ' }';
    access_log  /var/log/nginx/access.log  json;

# 开启文件的高效传输
    sendfile        on;

# 静态资源服务时建议开启
    #tcp_nopush     on;

# 动态资源服务时建议开启
    tcp_nodelay        on;
    keepalive_timeout  65;

# Gzip 压缩模块
    gzip  on;
    
    # 关闭 IE 6 版本的压缩（IE 6 不支持压缩）
    gzip_disable "MSIE [1-6]\\.";
    gzip_http_version 1.1;
    
# Virtal Server 
    include /etc/nginx/conf.d/*.conf;

}
```

更详细的 Nginx 参数性能优化参考 https://segmentfault.com/a/1190000017933715

### 优化 Nginx 进程数量

配置参数如下：

```bash
worker_processes 1; # 指定 Nginx 要开启的进程数，结尾的数字就是进程的个数，可以为 auto
```

这个参数调整的是 Nginx 服务的 worker 进程数，Nginx 有 Master 进程和 worker 进程之分，Master 为管理进程、真正接待“顾客”的是 worker 进程。

进程个数的策略：worker 进程数可以设置为等于 CPU 的核数。高流量高并发场合也可以考虑将进程数提高至 CPU 核数 x 2。这个参数除了要和 CPU 核数匹配之外，也与硬盘存储的数据及系统的负载有关，设置为 CPU 核数是个好的起始配置，也是官方建议的。

当然，如果想省麻烦也可以配置为`worker_processes auto;`，将由 Nginx 自行决定 worker 数量。当访问量快速增加时，Nginx 就会临时 fork 新进程来缩短系统的瞬时开销和降低服务的时间。

### 将不同的进程绑定到不同的CPU

默认情况下，Nginx 的多个进程有可能运行在同一个 CPU 核上，导致 Nginx 进程使用硬件的资源不均，这就需要制定进程分配到指定的 CPU 核上处理，达到充分有效利用硬件的目的。配置参数如下：

```bash
worker_processes 4;
worker_cpu_affinity 0001 0010 0100 1000;
```

其中 `worker_cpu_affinity` 就是配置 Nginx 进程与 CPU 亲和力的参数，即把不同的进程分给不同的 CPU 核处理。这里的`0001 0010 0100 1000`是掩码，分别代表第1、2、3、4核CPU。上述配置会为每个进程分配一核CPU处理。

当然，如果想省麻烦也可以配置`worker_cpu_affinity auto;`，将由 Nginx 按需自动分配。

### Nginx 事件处理模型优化

Nginx 的连接处理机制在不同的操作系统中会采用不同的 I/O 模型，在 linux 下，Nginx 使用 epoll 的 I/O 多路复用模型，在 Freebsd 中使用 kqueue 的 I/O 多路复用模型，在 Solaris 中使用 /dev/poll 方式的 I/O 多路复用模型，在 Windows 中使用 icop，等等。

配置如下：

```bash
events {
  use epoll;
}
```

`events` 指令是设定 Nginx 的工作模式及连接数上限。`use`指令用来指定 Nginx 的工作模式。Nginx 支持的工作模式有 select、 poll、 kqueue、 epoll 、 rtsig 和/ dev/poll。当然，也可以不指定事件处理模型，Nginx 会自动选择最佳的事件处理模型。

### 单个进程允许的客户端最大连接数

通过调整控制连接数的参数来调整 Nginx 单个进程允许的客户端最大连接数。

```bash
events {
  worker_connections 20480;
}
```

`worker_connections` 也是个事件模块指令，用于定义 Nginx 每个进程的最大连接数，默认是 1024。

最大连接数的计算公式如下：

```bash
max_clients = worker_processes * worker_connections;
```

如果作为反向代理，因为浏览器默认会开启 2 个连接到 server，而且 Nginx 还会使用fds（file descriptor）从同一个连接池建立连接到 upstream 后端。则最大连接数的计算公式如下：

```bash
max_clients = worker_processes * worker_connections / 4;
```

另外，**进程的最大连接数受 Linux 系统进程的最大打开文件数限制**，在执行操作系统命令 `ulimit -HSn 65535`或配置相应文件后， `worker_connections` 的设置才能生效。

### 配置获取更多连接数

默认情况下，Nginx 进程只会在一个时刻接收一个新的连接，我们可以配置`multi_accept` 为 `on`，实现在一个时刻内可以接收多个新的连接，提高处理效率。该参数默认是 `off`，建议开启。

```bash
events {
  multi_accept on;
}
```

### 配置 worker 进程的最大打开文件数

调整配置 Nginx worker 进程的最大打开文件数，这个控制连接数的参数为`worker_rlimit_nofile`。该参数的实际配置如下:

```bash
worker_rlimit_nofile 65535;
```

可设置为系统优化后的 `ulimit -HSn` 的结果。

### 优化域名的散列表大小

```bash
http {
  server_names_hash_bucket_size 128;
}
```

参数作用:设置存放域名( server names)的最大散列表的存储桶( bucket)的大小。 默认值依赖 CPU 的缓存行。

`server_names_hash_bucket_size` 的值是不能带单位 的。配置主机时必须设置该值，否则无法运行 Nginx，或者无法通过测试 。 该设置与 `server_ names_hash_max_size` 共同控制保存服务器名的 hash 表， hash bucket size 总是等于 hash 表的大小， 并且是一路处理器缓存大小的倍数。若 hash bucket size 等于一路处理器缓存的大小，那么在查找键时， 最坏的情况下在内存中查找的次数为 2。第一次是确定存储单元的地址，第二次是在存储单元中查找键值 。 若报 出 hash max size 或 hash bucket size 的提示，则需要增加 `server_names_hash_max size` 的值。

### TCP 优化

```bash
http {
  sendfile on;
  tcp_nopush on;

  keepalive_timeout 120;
  tcp_nodelay on;
}
```

第一行的 `sendfile` 配置可以提高 Nginx 静态资源托管效率。sendfile 是一个系统调用，直接在内核空间完成文件发送，不需要先 read 再 write，没有上下文切换开销。

TCP_NOPUSH 是 FreeBSD 的一个 socket 选项，对应 Linux 的 TCP_CORK，Nginx 里统一用 `tcp_nopush` 来控制它，并且只有在启用了 `sendfile` 之后才生效。启用它之后，数据包会累计到一定大小之后才会发送，减小了额外开销，提高网络效率。

TCP_NODELAY 也是一个 socket 选项，启用后会禁用 Nagle 算法，尽快发送数据，某些情况下可以节约 200ms（Nagle 算法原理是：在发出去的数据还未被确认之前，新生成的小数据先存起来，凑满一个 MSS 或者等到收到确认后再发送）。Nginx 只会针对处于 keep-alive 状态的 TCP 连接才会启用 `tcp_nodelay`。

### 优化连接参数

```bash
http {
  client_header_buffer_size 32k;
  large_client_header_buffers 4 32k;
  client_max_body_size 1024m;
  client_body_buffer_size 10m;
}
```

这部分更多是更具业务场景来决定的。例如`client_max_body_size`用来决定请求体的大小，用来限制上传文件的大小。上面列出的参数可以作为起始参数。

### 配置压缩优化

**Gzip 压缩**

我们在上线前，代码（JS、CSS 和 HTML）会做压缩，图片也会做压缩（PNGOUT、Pngcrush、JpegOptim、Gifsicle 等）。对于文本文件，在服务端发送响应之前进行 GZip 压缩也很重要，通常压缩后的文本大小会减小到原来的 1/4 - 1/3。

```bash
http {
  gzip on;
  gzip_buffers 16 8k;
  gzip_comp_level 6;
  gzip_http_version 1.0;
  gzip_min_length 1000;
  gzip_proxied any;
  gzip_vary on;
  gzip_types
    text/xml application/xml application/atom+xml application/rss+xml application/xhtml+xml image/svg+xml
    text/javascript application/javascript application/x-javascript
    text/x-json application/json application/x-web-app-manifest+json
    text/css text/plain text/x-component
    font/opentype application/x-font-ttf application/vnd.ms-fontobject
    image/x-icon;
  gzip_disable "MSIE [1-6]\\.(?!.*SV1)";
}
```

这部分内容比较简单，只有两个地方需要解释下：

`gzip_vary` 用来输出 Vary 响应头，用来解决某些缓存服务的一个问题，详情请看我之前的博客：HTTP 协议中 Vary 的一些研究。

`gzip_disable` 指令接受一个正则表达式，当请求头中的 UserAgent 字段满足这个正则时，响应不会启用 GZip，这是为了解决在某些浏览器启用 GZip 带来的问题。

默认 Nginx 只会针对 HTTP/1.1 及以上的请求才会启用 GZip，因为部分早期的 HTTP/1.0 客户端在处理 GZip 时有 Bug。现在基本上可以忽略这种情况，于是可以指定 gzip_http_version 1.0 来针对 HTTP/1.0 及以上的请求开启 GZip。

**Brotli 压缩**

Brotli 是基于LZ77算法的一个现代变体、霍夫曼编码和二阶上下文建模。Google软件工程师在2015年9月发布了包含通用无损数据压缩的Brotli增强版本，特别侧重于HTTP压缩。其中的编码器被部分改写以提高压缩比，编码器和解码器都提高了速度，流式API已被改进，增加更多压缩质量级别。

需要安装`libbrotli`、`ngx_brotli`，重新编译 Nginx 时，带上`--add-module=/path/to/ngx_brotli`即可，然后配置如下

```bash
http {
  brotli on;
  brotli_comp_level 6;
  brotli_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript application/javascript image/svg+xml;
}
```

Brotli 可与 Gzip 共存在一个配置文件中。

### 静态资源优化

静态资源优化，可以减少连接请求数，同时也不需要对这些资源请求打印日志。但副作用是资源更新可能无法及时。

```bash
server {
    # 图片、视频
    location ~ .*\\.(gif|jpg|jpeg|png|bmp|swf|flv|mp4|ico)$ {
      expires 30d;
      access_log off;
    }
    # 字体
    location ~ .*\\.(eot|ttf|otf|woff|svg)$ {
      expires 30d;
      access_log off;
    }
    # js、css
    location ~ .*\\.(js|css)?$ {
      expires 7d;
      access_log off;
    }
}
```



# 制作内部网络 yum 仓库

安装所需的软件 `createrepo nginx`

```bash
yum install createrepo nginx -y
```

配置 nginx 索引模块

```bash
cat > /etc/nginx/conf.d/yum.conf << EOF
server {
    listen       80 default_server;
    server_name  yum.alan.com;
    location / {
        autoindex on;
        autoindex_exact_size off;
        autoindex_localtime on;
        charset utf-8,gbk;
        root    /yum;
    }
}
EOF
```

准备软件仓库目录并下载需要的软件至该目录

```bash
yum install --downloadonly --downloaddir=/yum nginx screen httpd -y
```

生成 yum 元数据

```bash
createrepo /yum
```

客户端生成本地源

```bash
cat > /etc/yum.repos.d/local.repo << EOF
[local]
name=local
enable=1
gpgcheck=0
baseurl=http://172.16.1.11
```

客户端测试安装

```bash
yum makecache
yum install httpd
```

更新软件包的操作步骤：

第一种方法：真实下载

1.打开 yum 缓存

```bash
grep "keepcache" /etc/yum.conf 
keepcache=1
```

2.清空原来的缓存

```bash
yum clean all 
```

3.下载软件

```
yum remove php-mysql-5.4 php php-fpm php-common
rpm -Uvh <https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm>
rpm -Uvh <https://mirror.webtatic.com/yum/el7/webtatic-release.rpm>
yum install php71w php71w-cli php71w-common php71w-devel php71w-embedded php71w-gd php71w-mcrypt php71w-mbstring php71w-pdo php71w-xml php71w-fpm php71w-mysqlnd php71w-opcache -y
```

4.移动已经缓存下来的 rpm 包到 yum 仓库目录

```bash
find /var/cache/yum/ -type f -name "*.rpm"|xargs mv -t  /yum/
```

5.生成新的 yum 元数据

```bash
createrepo --update  /yum/
```

第二种方法：只下载不安装

```bash
yum install --downloadonly --downloaddir= /yum php71w php71w-cli php71w-common php71w-devel php71w-embedded php71w-gd php71w-mcrypt php71w-mbstring php71w-pdo php71w-xml php71w-fpm php71w-mysqlnd php71w-opcache
```
