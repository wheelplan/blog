---
title: Redis 基础入门
date: 2018-11-23 01:01:52
top_img: https://download.rocc.top/img/redis-header-img-001.png
keywords: Redis 基础 集群 哨兵模式 主从复制 非关系型数据库 NoSQL
tags:
 - redis

---

# Redis 非关系型数据库

<br>

`Redis` 是一种基于 **键值对** 的 `NoSQL` 数据库。与很多键值对数据库不同，`Redis` 提供了丰富的 **值数据存储结构**，包括 `string`(**字符串**)、`hash`(**哈希**)、`list`(**列表**)、`set`(**集合**)、`zset`(**有序集合**)、`bitmap`(**位图**)等等。

<br>

## 常见的数据库类型

关系型: mysql oracle 

非关系型: mongo redis ES



## Redis 的重要特性

### 1.速度快, 读写性能优异

Redis 所有的数据都存放在内存中 

Redis 使用 c 语言实现 

Redis 使用单线程架构 



### 2.基于键值对的数据结构,支持多种数据类型

 5 种数据类型 : 字符串, 哈希, 列表, 集合, 有序集合



### 3.丰富的功能

提供了键过期功能,可以实现缓存

提供了发布订阅功能,可以实现消息系统

提供了 pipeline 功能,客户端可以将一批命令一次性传到 Redis,减少了网络开销 



### 4.简单稳定

源码较少,3.0 版本以后 `5` 万行代码左右 

使用单线程模型法,是的 Redis 服务端处理模型变得简单

不依赖操作系统的中的类库 



### 5.客户端语言多

java,PHP,python,C,C++,Nodejs 等 



### 6.数据持久化

 `RDB` 和 `AOF`



### 7.主从复制 

分布式的基础



### 8.高可用和分布式

哨兵 redis-sentinel 

集群 redis-cluster 



## Redis 的应用场景

### 1.缓存-键过期时间

缓存 `session` 会话, 过期删除

缓存用户信息, 缓存 MySQL 部分数据, 用户先访问 Redis, 若 Redis 没有则再访问 mysql, 然后回写到 redis

商城优惠卷过期时间



### 2.排行榜-列表&有序集合

热度排行

活动积分排行



### 3.计数器应用-天然支持计数器

浏览数、播放次数、评论数、点赞数 



### 4.社交网络-集合

粉丝，共同好友，兴趣爱好，标签，推送，下拉刷新等是社交网站必备的功能



### 5.消息队列系统-发布订阅

发布订阅（`PUB/SUB`）和 阻塞队列 的功能，虽然和专业的消息队列比，还不够强大，但对于一般的消息队列功能基本满足。如配合 ELK 实现日志收集。
<br>


# Redis 安装部署

## 源码编译安装

```bash
# 下载源码包
cd /tmp && wget https://download.rocc.top/packages/redis/redis-5.0.7.tar.gz

# 解压
tar xf redis-5.0.7.tar.gz

# 进入目录
cd redis-5.0.7/

# 安装依赖
yum install gcc gcc-c++

# 编译并安装
make && make install

# 创建 redis 用户
useradd -s /sbin/nologin -M redis

# 创建 redis 数据目录
mkdir /var/{lib,log,run}/redis -p
chown -R redis: /var/{lib,log,run}/redis

# 创建配置文件
cat > /etc/redis.conf << EOF
# 以守护进程模式启动
daemonize yes
#绑定的主机地址
bind 127.0.0.1 `ifconfig eth0 | awk 'NR==2{print $2}'`
# 监听端口
port 6379
# pid 文件和 log 文件地址
pidfile /var/run/redis/redis.pid
logfile /var/log/redis/redis.log
# 设置数据库的数量，默认数据库为 0
databases 16
# 指定本地持久化文件的文件名,默认是 dump.rdb
dbfilename redis.rdb
save 900 1
save 300 10
save 60 10000
# 本地数据库的目录,默认是 ./
dir /var/lib/redis
# AOF 重写
appendonly yes
appendfilename redis.aof
appendfsync everysec
# 配置连接密码
requirepass menglu
EOF

# 配置 systemd 启动服务
cat > /usr/lib/systemd/system/redis.service << EOF
[Unit]
Description=Redis persistent key-value database
After=network.target
After=network-online.target
Wants=network-online.target

[Service]
ExecStart=/usr/local/bin/redis-server /etc/redis.conf --supervised systemd
ExecStop=/usr/local/bin/redis-cli -p 6379 shutdown
Type=notify
User=redis
Group=redis
RuntimeDirectory=redis
RuntimeDirectoryMode=0755

[Install]
WantedBy=multi-user.target
EOF

# 启动服务并设置为开机自启
systemctl start redis && systemctl enable redis

# 进入客户端
redis-cli

# 在客户端中关闭服务
# shutdown

# 关闭服务
# redis-cli shutdown
```

<br>

## 配置文件主要参数说明

```
# redis 进程是否以守护进程的方式运行，yes 为是，no 为否(不以守护进程的方式运行会占用一个终端)。
daemonize yes
# 指定 redis 进程的 PID 文件存放位置
pidfile /var/run/redis.pid
# redis 进程的端口号
port 6379
# 绑定的主机地址
bind 127.0.0.1
# 客户端闲置多长时间后关闭连接，默认此参数为 0 即关闭此功能
timeout 300
# redis 日志级别，可用的级别有debug.verbose.notice.warning
loglevel verbose
# log 文件输出位置，如果进程以守护进程的方式运行，此处又将输出文件设置为 stdout 的话，就会将日志信息输出到 /dev/null
logfile /var/log/redis.log
# 设置数据库的数量，默认为 0 可以使用 select <dbid> 命令在连接上指定数据库 id
databases 16
# 指定在多少时间内刷新次数达到多少的时候会将数据同步到数据文件
save <seconds> <changes>
# 指定存储至本地数据库时是否压缩文件，默认为 yes 即启用存储
rdbcompression yes
# 指定本地数据库文件名
dbfilename dump.db
# 指定本地数据存放位置
dir ./
# 指定当本机为 slave 服务时，设置 master 服务的 IP 地址及端口，在 redis 启动的时候他会自动跟 master 进行数据同步
slaveof <masterip> <masterport>
# 当 master 设置了密码保护时，slave 服务连接 master 的密码
masterauth <master-password>
# 设置 redis 连接密码，如果配置了连接密码，客户端在连接 redis 是需要通过 AUTH <password> 命令提供密码，默认关闭
requirepass footbared
# 设置同一时间最大客户连接数，默认无限制。redis 可以同时连接的客户端数为 redis 程序可以打开的最大文件描述符，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis 会关闭新的连接并向客户端返回 max number of clients reached 错误信息
maxclients 128
# 指定 Redis 最大内存限制，Redis 在启动时会把数据加载到内存中，达到最大内存后，Redis 会先尝试清除已到期或即将到期的 Key。当此方法处理后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis 新的vm 机制，会把 Key 存放内存，Value 会存放在 swap 区
maxmemory <bytes>
# 指定是否在每次更新操作后进行日志记录，Redis 在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis 本身同步数据文件是按上面 save 条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为 no。
appendonly no
# 指定跟新日志文件名默认为 appendonly.aof
appendfilename appendonly.aof
# 指定更新日志的条件，有三个可选参数 no：表示等操作系统进行数据缓存同步到磁盘(快)，always：表示每次更新操作后手动调用 fsync() 将数据写到磁盘(慢，安全)， everysec：表示每秒同步一次(折衷，默认值)；
appendfsync everysec
```

<br>

## 快速在多台主机上部署 Redis

```bash
# 将 redis 二进制文件复制到目标主机
scp /usr/local/bin/redis-* 10.0.1.3:/usr/local/bin/

# 创建 redis 用户
useradd -s /sbin/nologin -M redis

# 创建 redis 数据目录
mkdir /var/{lib,log,run}/redis -p
chown -R redis: /var/{lib,log,run}/redis

# 创建配置文件
cat > /etc/redis.conf << EOF
# 以守护进程模式启动
daemonize yes
#绑定的主机地址
bind 127.0.0.1 `ifconfig eth0 | awk 'NR==2{print $2}'`
# 监听端口
port 6379
# pid 文件和 log 文件地址
pidfile /var/run/redis/redis.pid
logfile /var/log/redis/redis.log
# 设置数据库的数量，默认数据库为 0
databases 16
# 指定本地持久化文件的文件名,默认是 dump.rdb
dbfilename redis.rdb
save 900 1
save 300 10
save 60 10000
# 本地数据库的目录,默认是 ./
dir /var/lib/redis
# AOF 重写
appendonly yes
appendfilename redis.aof
appendfsync everysec
# 配置连接密码
requirepass menglu
EOF

# 配置 systemd 启动服务
cat > /usr/lib/systemd/system/redis.service << EOF
[Unit]
Description=Redis persistent key-value database
After=network.target
After=network-online.target
Wants=network-online.target

[Service]
ExecStart=/usr/local/bin/redis-server /etc/redis.conf --supervised systemd
ExecStop=/usr/local/bin/redis-cli -p 6379 shutdown
Type=notify
User=redis
Group=redis
RuntimeDirectory=redis
RuntimeDirectoryMode=0755

[Install]
WantedBy=multi-user.target
EOF

# 启动服务并设置为开机自启
systemctl start redis && systemctl enable redis
```



# Redis 基础命令

1.**DBSIZE** 查看 `key` 的数量

```
dbsize
```

2.**慎用 KEYS** 查看所有的 `key`

```
keys *
```

3.**EXISTS** 查看给定的 `key` 是否存在

返回值 `n` 表示有的 `n` 个 `key` 存在

```
exists alan
exists alan yuiya
```

4.**DEL** 删除一个或多个 `key`

返回值 `n` 表示成功删除了 `n` 个 `key`

```
del k1
del k1 k2 k3
```

5.**EXPIRE** 设置给定 `key` 的过期时间。

过期后将不再可用。单位为秒。设置成功返回 `1` 。

```
expire k1 100
```

6.**PERSIST** 取消给定 `key` 过期时间。

取消成功返回 `1` 。如果 `key` 不存在或 `key` 没有设置过期时间则返回 `0`。

```
persist k1
```

7.**TTL** 查看 `key` 的剩余过期时间。单位为秒。

返回值为 `-1` 时表示该 `key` 没有设置过期时间。

返回值为 `-2` 时表示该 `key` 不存在。

```
ttl k1
```

<br>

## 字符串 ( String )

1.**SET** 设置一个 `key`

```
set k1 1
```

2.**MSET** 设置多个 `key`

```
mset k1 1 k2 2
```

3.**GET** 查看一个 `key`

```
get k1
```

4.**MGET** 查看多个 `key`

```
mget k1 k2
```

5.**INCR** 将 key 中的数值增加 `1`

```
incr k1
```

6.**INCRBY** 将 `key` 中的数值增加 `n`

```
incrby k1 511
```

7.**DECR** 将 `key` 中的数值减去 `1`

```
decr k1
```

8.**DECRBY** 将 `key` 中的数值减去 `n`

```bash
decrby k1 511
```

9.**GETRANGE** 获取 `key` 值的指定部分，字符串的截取范围由 `start` 和 `end` 两个偏移量决定 (包括 `start` 和 `end` )

```bash
set a alan
getrange a 0 2
"ala"
```

10. **SETRANGE** 用指定的字符串，从指定的起始位置开始，覆盖给定 key 所储存的字符串值

```bash
set a alan
setrange a 0 ttt
(integer) 4
get a
"tttn"
```

<br>

## 列表 ( List )

1.**LPUSH** 从列表 左侧 插入数据

```bash
lpush list a b
```

2.**RPUSH** 从列表 右侧 依次插入数据

```
rpush list c d e f
```

3.**LLEN** 查看列表长度

```
 llen list
```

4.**LRANGE** 查看列表中指定区间的元素

0 代表第一个元素, 1 代表第二个元素, 以此类推 ; -1 代表最后一个元素

查看列表中的所有元素

```
lrange list 2 5
```

查看从第 3 个元素开始到第 6 个元素之间的 4 个元素

```
lrange list 0 -1
```

5.**LPOP** 从列表 左边 删除一个元素

```
 lpop list
```

6.**RPOP** 从列表 右边 删除一个元素

```
rpop list
```

7.**DEL** 删除列表

```
del list
```

<br>

## 哈希 ( Hash )

例如 : 数据库中有一张表 `user` 包含 `id`, `name`, `age`, `sex` 四个属性

```bash
	id  name  age  sex
1、	1  小明   16   1
2、	2  小红   20   0
3、	3  小军   18   1
```

如果要整表缓存到 `redis` 中则使用 `hash`，一条数据一个 `hash`, 一个 `hash` 里则包含 `4` 个 `filed` 。

```bash
      key     field1 value1  field2 value2  field3 value3 field value
hmset user_1  id     1       name   小明    age    16     sex   1
hmset user_2  id     2       name   小红    age    20     sex   0
hmset user_3  id     3       name   小军    age    18     sex   1
```

<br>

1.**HSET** 给哈希表 `key` 中的域 `field` 赋值

如果给定的哈希表并不存在， 那么一个新的哈希表将被创建并执行 `HSET` 操作。

如果域 `field` 已经存在于哈希表中， 那么它的旧值将被新值 `value` 覆盖 。

```bash
hset myhash rocc linux.rocc.top
```

当 `HSET` 命令在哈希表中新创建 `field` 域并成功为它设置值时， 命令返回 `1` ； 如果域 `field` 已经存在于哈希表， 并且 `HSET` 命令成功使用新值覆盖了它的旧值， 那么命令返回 `0` 。

2.**HMSET** 设置一个或多个域值对 `field-value` 到哈希表中。

如果哈希表不存在，会创建一个新的哈希表，并执行 `HMSET` 操作。

如果域 `field` 已经存在于哈希表中， 那么它的旧值将被新值 `value` 覆盖 。

```bash
hmset myhash rocc rocc.top google google.com
```

如果命令执行成功，返回 `OK` 。当 `key` 不是哈希表类型时，返回一个错误。

3.**HMGET** 返回哈希表 `key` 中，一个或多个给定域的值。

```bash
hmget myhash rocc google
```

如果给定的域不存在于哈希表，那么返回一个 `nil` 值。

对一个不存在的 `key` 进行 `HMGET` 操作会返回一个只带有 `nil` 值的表。

4.**HGETALL** 返回哈希表 `key` 中，所有的域和值。

```bash
hgetall myhash
```

<br>

## 集合 ( Set )

  集合中不会出现重复的值，自动去重。

1.**SADD** 添加一个或多个 `member` 元素到集合 `key` 当中，已经存在于集合的 `member` 元素将被忽略。

```bash
sadd set1 1 2 3
sadd set2 1 3 5 7 
```

​	当 `key` 不存在时，则会先创建再添加元素。

2.**SMEMBERS** 返回集合 `key` 中的所有成员。

```bash
smembers set1
smembers set2
```

3.**SINTER** 查看集合的交集。

```bash
sinter set1 set2
```

4.**SUNION** 查看集合的并集。

```bash
sunion set1 set2
```

5.**SDIFF** 查看集合的差集。

```bash
sdiff set1 set2
```

6.**SCARD** 查看一个集合中的元素数量。

```
scard set1
```

7.**SREM** 删除一个或多个成员。

```bash
srem set1 1 2
```

<br>

## 有序集合 ( zset )

1.**ZADD** 添加一个或多个 `member` 元素及其 `score` 值到有序集合 `key` 中。

如果某个 `member` 已经是有序集的成员，那么更新这个 `member` 的 `score` 值，并通过重新插入这个 `member` 元素，来保证该 `member` 在正确的位置上。`score` 值可以是整数值或双精度浮点数。

```
zadd sz 99 alan 100 yuiya
```

2.**ZSCORE** 查看某个成员的 `score` 值。

```
zscore sz alan
zscore sz yuiya
```

3.**ZCARD** 查看一个有序集合中元素的数量。

```
zcard sz
```

4.**ZCOUNT** 查看有序集合中，`score` 值在 `min` 和 `max` 之间 (默认包括`min` 和 `max`) 的成员的数量。

```
zcount sz 99 100
```

5.**ZRANGE** 查看有序集合中，指定区间内的成员。

成员的位置按 `score` 值递增 (从小到大) 来排序。

具有相同 `score` 值的成员按字典序来排列。

以 `0` 表示有序集第一个成员，以 `1` 表示有序集第二个成员，以此类推。 

负数下标，以 `-1` 表示最后一个成员，`-2` 表示倒数第二个成员，以此类推。

```
zrange sz 0 -1
```

6.**ZRANGEBYSCORE** 查看有序集合中，所有 `score` 值介于 `min` 和 `max` 之间 (包括`min` 和 `max`) 的成员。

```
zrangebyscore sz 100 100
```

7.**ZREVRANGE** 查看有序集合中，指定区间内的成员。

成员的位置按 `score` 值递减 (从大到小) 来排序。

```
zrevrange sz 0 -1
```

8.**ZRANK** 查看有序集合中某个成员 `member` 的排名。

其中有序集成员按 `score` 值递增 (从小到大) 顺序排列。

`score` 值最小的成员排名为 `0` 。

```
zrank sz alan
```

9.**ZREVRANK** 查看有序集合中某个成员 `member` 的排名。

其中有序集成员按 `score` 值递减 (从大到小) 顺序排列。

`score` 值最大的成员排名为 `0` 。

```
zrevrank sz yuiya
```

10.**ZINCRBY** 为有序集合中的成员 `member` 的 `score` 值加上增量 `increment` 。

可以通过传递一个负数值 `increment` ，让 `score` 减去相应的值。

当 `key` 不存在，或 `member` 不是 `key` 的成员时， `ZINCRBY key increment member` 等同于 `ZADD key increment member` 。

`score` 和 `increment` 的值可以是整数值或双精度浮点数。

```
zincrby sz 1 alan
```

11.**ZREM** 删除有序集合中的一个或多个成员。

```bash
zrem sz alan
```

<br>

# Redis 用户认证

```
# 1.在配置文件中设置密码
requirepass linux

# 2.使用密码登陆的两种方式：
# 方式一
redis-cli 
auth linux
set k1 v1
# 方式二
redis-cli -a linux get k1

# 禁用或重命名危险命令
rename-command CONFIG ""
rename-command KEYS ""
rename-command SHUTDOWN ""
rename-command FLUSHALL ""
rename-command DEL ""
rename-command FLUSHDB ""
```



# Redis 持久化

持久化是最简单的高可用方法。它的主要作用是 **数据备份**，即将数据存储在 **磁盘**，保证数据不会因进程退出而丢失。Redis 为持久化提供了 RDB 和 AOF 两种方式。

Redis 内部存在一个定时任务机制，定时任务执行的频率可以在配置文件中通过 hz 10 来设置（这个配置表示 1s 内执行 10 次，即每 100ms 触发一次定时任务）。该值最大能够设置为：500，但是不建议超过：100，因为值越大说明执行频率越频繁越高，这会带来 CPU 的更多消耗，从而影响主进程读写性能。

定时任务使用的是 Redis 自己实现的 **TimeEvent**，它会定时去调用一些命令完成定时任务，这些任务可能会阻塞主进程导致 Redis 性能下降。因此我们在配置 Redis 时，一定要整体考虑一些会触发定时任务的配置，根据实际情况进行调整。

<br>

## RDB  方式持久化

**RDB**：在指定的时间间隔对数据进行快照存储。

优点：文件简洁，恢复速度快，适合做备份。

缺点：实时性差，容易造成数据的丢失。

```bash
# RDB 文件名，默认为 dump.rdb
dbfilename dump.rdb

# 文件存放的目录，AOF 文件同样存放在此目录。默认为当前工作目录
dir ./

# 指定在每 N 秒后数据发生了 M 次改变就保存快照文件
save 900 1		# 900 秒内如果有 1 条是写入命令，就触发产生一次快照
save 300 10 	# 300 秒内如果有 10 条是写入命令，就触发产生一次快照
save 60 10000 	# 60  秒内如果有 10000 条是写入命令，就触发产生一次快照

# 默认 Redis 会采用 LZF 对数据进行压缩。如果你想节省点 CPU 的性能，就可以把压缩功能禁用掉，但是数据集就会比没压缩时大
rdbcompression yes

# 如果持久化出错，主进程是否停止写入
stop-writes-on-bgsave-error yes

# 导入时是否检查，从版本 5.0 的 RDB 开始，一个 CRC64 的校验码会放在文件的末尾。这样更能保证文件的完整性，但是在保存或者加载文件时会损失一定的性能（大概10%）。如果想追求更高的性能，可以把它禁用掉，这样文件在写入校验码时会用 0 替代，加载的时候看到 0 就会直接跳过校验。
rdbchecksum yes
```

<br>

在 Redis 中 RDB 持久化的触发分为两种：自己手动触发与 Redis 定时触发。

**针对 RDB 方式的持久化，手动触发可以使用：**

- save：会阻塞当前 Redis 服务器，直到持久化完成，线上应该禁止使用。
- bgsave：该触发方式会 `fork` 一个子进程，由子进程负责持久化过程，因此阻塞只会发生在 `fork` 子进程的时候。

**自动触发的场景主要是有以下几点：**

- 根据 `save m n` 配置规则自动触发；
- 从节点全量复制时，主节点发送 `rdb` 文件给从节点完成复制操作，主节点会触发 `bgsave`；
- 执行 `debug reload` 时；
- 执行 `shutdown`,`kill`,`pkill` 时都会自动触发 `bgsave`。

需要注意的是 `fork` 操作会阻塞，导致 Redis 读写性能下降。我们可以控制单个 Redis 实例的最大内存，来尽可能降低Redis 在 `fork` 时的事件消耗。也可以通过控制自动触发的频率减少 `fork` 次数，或者使用手动触发，根据自己的机制来完成持久化。

<br>

<br>

## AOF  方式持久化

**AOF**：以追加的方式记录每次对服务器写的操作，当服务器重启的时候会重新执行这些命令来恢复原始的数据。

优点：安全，在默认配置下最多损失 1 秒的数据，aof 文件简单易读。

缺点：文件较大，恢复速度慢。

```bash
# 是否启用 AOF
appendonly yes

# aof 文件存放目录，与 rdb 共用。默认为当前工作目录
dir ./

# 默认文件名为 appendonly.aof
appendfilename "appendonly.aof"

# 同步方式 (调用 fsync)
# fsync 函数只对由文件描述符 filedes 指定的单一文件起作用，并且等待写磁盘操作结束，然后返回。fsync 可用于数据库这样的应用程序，这种应用程序需要确保将修改过的块立即写到磁盘上。
# appendfsync no	    # 从不调用 fsync，交给 OS 来处理，非常快但最不安全
# appendfsync always 	# 每当有新命令时立即写入到 AOF 
appendfsync everysec 	# 每秒同步(fsync)一次，默认推荐方式

# 如果启用 aof-load-truncated，在加载时发现 aof 尾部不正确时，会向客户端写入一个 log，但是会继续执行，如果设置为 no ，发现错误就会停止，必须修复后才能重新加载
aof-load-truncated yes
```

<br>

AOF 的整个流程大体来看可以分为两步，一步是命令的实时写入（如果是 `appendfsync everysec` 配置，会有 1s 损耗），第二步是对 aof 文件的重写。

对于实时写入这一步主要的流程是：命令写入 ==> 追加到 aof_buf ==> 同步到 aof 磁盘。那么这里为什么要先写入 buf 在同步到磁盘呢？如果实时写入磁盘会带来非常高的磁盘 IO，影响整体性能。

aof 重写是为了减少 aof 文件的大小，可以手动或者自动触发。`fork` 的操作也是发生在重写这一步，这里也会对主进程产生阻塞。

**手动触发：** `bgrewriteaof`，**自动触发** 就是根据配置规则来触发，当然自动触发的整体时间还跟 Redis 的定时任务频率有关系。

1. 在重写期间，由于主进程依然在响应命令，为了保证最终备份的完整性；因此它依然会写入旧的 AOF file 中，如果重写失败，能够保证数据不丢失。
2. 为了把重写期间响应的写入信息也写入到新的文件中，因此也会为子进程保留一个 buf，防止新写的 file 丢失数据。
3. 重写是直接把当前内存的数据生成对应命令，并不需要读取旧的 AOF 文件进行分析、命令合并。
4. AOF 文件直接采用的文本协议，主要是兼容性好、追加方便、可读性高可认为修改修复。

> 无论是 RDB 还是 AOF 都是先写入一个临时文件，然后通过 `rename` 完成文件的替换工作。

<br>

## 性能与实践

RDB 的快照和 AOF 的重写都需要 fork，这是一个重量级操作，会对 Redis 造成阻塞。因此为了不影响 Redis 主进程响应，我们需要尽可能降低阻塞。

1. 降低 fork 的频率，比如可以手动来触发 RDB 生成快照、与 AOF 重写；
2. 控制 Redis 最大使用内存，防止 fork 耗时过长；
3. 使用更高配置的硬件；
4. 合理配置 Linux 的内存分配策略，避免因为物理内存不足导致 fork 失败。

在线上我们到底该怎么做？

1. 如果 Redis 中的数据并不是特别敏感或者可以通过其它方式重写生成数据，可以关闭持久化，如果丢失数据可以通过其它途径补回；
2. 自己制定策略定期检查 Redis 的情况，然后可以手动触发备份、重写数据；
3. 单机如果部署多个实例，要防止多个实例同时运行持久化、重写操作，防止出现内存、CPU、IO 资源竞争，让持久化变为串行；
4. 可以加入主从机器，利用一台从机器进行备份处理，其它机器正常响应客户端的命令；
5. RDB 持久化与 AOF 持久化可以同时存在，配合使用。



# Redis 主从复制

复制是高可用 `Redis` 的基础，**哨兵** 和 **集群** 都是在 **复制基础** 上实现高可用的。复制主要实现了数据的多机备份以及对于读操作的负载均衡和简单的故障恢复。缺陷是故障恢复无法自动化、写操作无法负载均衡、存储能力受到单机的限制。

<br>

## 配置主从坏境

| 主机名 | 角色     | IP 地址  | 端口号 |
| ------ | :------- | :------- | :----- |
| db01   | Master   | 10.0.0.3 | 6379   |
| db02   | Slave-01 | 10.0.1.3 | 6379   |
| db03   | Slave-02 | 10.0.2.3 | 6379   |

<br>

### Master 主服务器 db01 配置

```bash
cat > /etc/redis.conf << EOF
daemonize yes
bind 10.0.0.3 127.0.0.1
port 6379
pidfile /var/run/redis/redis.pid
logfile /var/log/redis/redis.log
databases 16
dbfilename redis.rdb
dir /var/lib/redis
appendonly yes
appendfilename redis.aof
appendfsync everysec
masterauth menglu
requirepass menglu
EOF

systemctl restart redis
```

<br>

### Slave-01 从服务器 db02 配置

```bash
cat > /etc/redis.conf << EOF
daemonize yes
bind 10.0.1.3 127.0.0.1
port 6379
pidfile /var/run/redis/redis.pid
logfile /var/log/redis/redis.log
databases 16
dbfilename redis.rdb
dir /var/lib/redis
appendonly yes
appendfilename redis.aof
appendfsync everysec
masterauth menglu
requirepass menglu
slaveof 10.0.0.3 6379
EOF

systemctl restart redis
```

<br>

### Slave-02 从服务器 db03 配置

```bash
cat > /etc/redis.conf << EOF
daemonize yes
bind 10.0.2.3 127.0.0.1
port 6379
pidfile /var/run/redis/redis.pid
logfile /var/log/redis/redis.log
databases 16
dbfilename redis.rdb
dir /var/lib/redis
appendonly yes
appendfilename redis.aof
appendfsync everysec
masterauth menglu
requirepass menglu
slaveof 10.0.0.3 6379
EOF

systemctl restart redis
```

<br>

**临时开启复制**

```bash
redis-cli -h 10.0.1.3 slaveof 10.0.0.3 6379
redis-cli -h 10.0.2.3 slaveof 10.0.0.3 6379
```

**临时取消复制 **

```bash
redis-cli -h 10.0.1.3  slaveof no one
redis-cli -h 10.0.2.3  slaveof no one
```

<br>

## 主从复制流程

1. 从节点发送同步请求到主节点。
2. 主节点接收到从节点的请求之后,立即执行 bgsave 将当前内存里的数据持久化到磁盘上,完成之后,将 rdb 文件发送给从节点。
3. 从节点从主节点接收到 rdb 文件后,清空自己的数据,载入从主节点接收的 rdb 文件到自己的内存里。
4. 从节点与和主节点实时同步。



# Redis sentinel 哨兵模式

哨兵在主从复制的基础上，实现了 **自动化** 的 **故障恢复**。缺陷是 **写操作** 无法 **负载均衡**，**存储能力** 受到 **单机** 的限制。

<br>

![](https://download.rocc.top/img/image-20200311103320214.png)

<br>

| 主机名 | 角色             | IP 地址  | 端口号      |
| ------ | :--------------- | :------- | :---------- |
| db01   | Master，Sentinel | 10.0.0.3 | 6379，26379 |
| db02   | Slave，Sentinel  | 10.0.1.3 | 6379，26379 |
| db03   | Slave，Sentinel  | 10.0.2.3 | 6379，26379 |

<br>

## Redis Sentinel 搭建

**前提 : 主从复制已配置完成**

```bash
# 在三个节点上各执行一次
cat > /etc/redis-sentinel.conf << EOF
bind $(ifconfig eth0 | awk 'NR==2{print $2}')
port 26379
daemonize yes
logfile /var/log/redis/redis-sentinel.log
dir /var/lib/redis
sentinel monitor myredis 10.0.0.3 6379 2
sentinel down-after-milliseconds myredis 3000
sentinel parallel-syncs myredis 1
sentinel failover-timeout myredis 18000
sentinel auth-pass myredis menglu
EOF

chown -R redis: /etc/redis*

cat > /usr/lib/systemd/system/redis-sentinel.service << EOF
[Unit]
Description=Redis Sentinel
After=network.target
After=network-online.target
Wants=network-online.target

[Service]
ExecStart=/usr/local/bin/redis-sentinel /etc/redis-sentinel.conf --supervised systemd
ExecStop=/usr/local/bin/redis-cli -h  $(ifconfig eth0|awk 'NR==2{print $2}') -p 26379 shutdown
Type=notify
User=redis
Group=redis
RuntimeDirectory=redis
RuntimeDirectoryMode=0755

[Install]
WantedBy=multi-user.target
EOF

systemctl start redis-sentinel && systemctl enable redis-sentinel

# 检查各节点上的 master 地址是否一致
redis-cli -h 10.0.0.3 -p 26379 Sentinel get-master-addr-by-name myredis
redis-cli -h 10.0.1.3 -p 26379 Sentinel get-master-addr-by-name myredis
redis-cli -h 10.0.2.3 -p 26379 Sentinel get-master-addr-by-name myredis

# 在该配置下实现故障自动转移前提: sentinel 最多能宕掉 1 个, redis 节点最多可以宕掉 2 个。
```

<br>

## Redis Sentinel 的主要功能

`Sentinel` 的主要功能包括 **主节点存活检测**、**主从运行情况检测**、**自动故障转移**（`failover`）、**主从切换**。`Redis` 的 `Sentinel` 最小配置是 **一主一从**。

`Redis` 的 `Sentinel` 系统可以用来管理多个 `Redis` 服务器，该系统可以执行以下四个任务：

- **监控**

`Sentinel` 会不断的检查 **主节点** 和 **从节点** 是否正常运行。

- **通知**

当被监控的某个 `Redis` 节点出现问题，`Sentinel` 通过 `API` **脚本** 向 **管理员** 或者其他的 **应用程序** 发送通知。

- **自动故障转移**

当 **主节点** 不能正常工作时，`Sentinel` 会开始一次 **自动的** 故障转移操作，它会将与 **失效主节点** 是 **主从关系** 的其中一个 **从节点** 升级为新的 **主节点**，并且将其他的 **从节点** 指向 **新的主节点**。

- **配置提供者**

在 `Redis Sentinel` 模式下，**客户端应用** 在初始化时连接的是 `Sentinel` **节点集合**，从中获取 **主节点** 的信息。

<br>

给 redis 节点加权，来确定优先备选主节点

```bash
流程说明：
设置其他节点的权重为 0
手动发起重新选举
观察所有节点消息是否同步
观察切换结果是否符合预期

命令解释：
查询命令:CONFIG GET slave-priority
设置命令:CONFIG SET slave-priority 0
主动切换:sentinel failover myredis

操作命令：
redis-cli -h 10.0.0.3 -p 6379 CONFIG SET slave-priority 0
redis-cli -h 10.0.1.3 -p 6379 CONFIG SET slave-priority 0
redis-cli -h 10.0.2.3 -p 26379 sentinel failover myredis

验证选举结果：
redis-cli -h 10.0.0.3 -p 26379 Sentinel get-master-addr-by-name myredis
```



# Redis 集群

<br>

通过集群，`Redis` 可以解决 写操作无法 **负载均衡** 以及 **存储能力** 受到 **单机限制** 的问题，实现了较为 完善 的 高可用方案。`Redis Cluster` 集群模式通常具有 **高可用**、**可扩展性**、**分布式**、**容错** 等特性。

<br>

## **集群的基本概念**

redis 集群，无论有几个节点，一共只有16384个槽。

所有的槽位都必须分配，哪怕有 `1` 个槽位不正常，整个集群都无法不用。

每个节点的槽的顺序不重要，重点是数量。

hash 算法足够随机，足够平均。

每个槽被分配到数据的概率是相当的。

集群的高可用依赖于主从复制。

集群拥有自己的配置文件，动态更新，不要手动修改。

集群通讯会使用基础端口号 +10000 的端口，这个是自动创建的，不是配置文件配置的。

集群槽位分配比例允许误差在 2% 之间。

<br>

| 实例名          | IP 地址  | 端口号      |
| --------------- | :------- | :---------- |
| db01-redis-6380 | 10.0.0.3 | 6380，16380 |
| db01-redis-6381 | 10.0.0.3 | 6381，16381 |
| db02-redis-6380 | 10.0.1.3 | 6380，16380 |
| db02-redis-6381 | 10.0.1.3 | 6381，16381 |
| db03-redis-6380 | 10.0.2.3 | 6380，16380 |
| db03-redis-6381 | 10.0.2.3 | 6381，26381 |
| db01-redis-6390 | 10.0.0.3 | 6390，16390 |
| db01-redis-6391 | 10.0.0.3 | 6391，16391 |

<br>

## 部署集群

### 在每台主机(db01、db02、db03)上配置两个实例

```bash
# 部署 redis-6380
cat > /etc/redis-6380.conf << EOF
# 以守护进程模式启动
daemonize yes
#绑定的主机地址
bind `ifconfig eth0 | awk 'NR==2{print $2}'`
# 监听端口
port 6380
# pid 文件和 log 文件地址
pidfile /var/run/redis/redis-6380.pid
logfile /var/log/redis/redis-6380.log
# 设置数据库的数量，默认数据库为 0
databases 16
# 指定本地持久化文件的文件名,默认是 dump.rdb
dbfilename redis-6380.rdb
# 本地数据库的目录,默认是 ./
dir /var/lib/redis
# AOF 重写
appendonly yes
appendfilename redis-6380.aof
appendfsync everysec
# 开启集群
cluster-enabled yes
cluster-config-file cluster-6380.conf
cluster-node-timeout 15000
EOF

mkdir /var/{lib,log,run}/redis -p
chown -R redis: /var/{lib,log,run}/redis /etc/redis*

cat > /usr/lib/systemd/system/redis-6380.service << EOF
[Unit]
Description=Redis 6380
After=network.target
After=network-online.target
Wants=network-online.target

[Service]
ExecStart=/usr/local/bin/redis-server /etc/redis-6380.conf --supervised systemd
ExecStop=/usr/local/bin/redis-cli -h `ifconfig eth0 | awk 'NR==2{print $2}'` -p 6380 shutdown
Type=notify
User=redis
Group=redis
RuntimeDirectory=redis
RuntimeDirectoryMode=0755

[Install]
WantedBy=multi-user.target
EOF

systemctl start redis-6380 && systemctl enable redis-6380


# 部署 redis-6381
cat > /etc/redis-6381.conf << EOF
# 以守护进程模式启动
daemonize yes
#绑定的主机地址
bind `ifconfig eth0 | awk 'NR==2{print $2}'`
# 监听端口
port 6381
# pid 文件和 log 文件地址
pidfile /var/run/redis/redis-6381.pid
logfile /var/log/redis/redis-6381.log
# 设置数据库的数量，默认数据库为 0
databases 16
# 指定本地持久化文件的文件名,默认是 dump.rdb
dbfilename redis-6381.rdb
# 本地数据库的目录,默认是 ./
dir /var/lib/redis
# AOF 重写
appendonly yes
appendfilename redis-6381.aof
appendfsync everysec
# 开启集群
cluster-enabled yes
cluster-config-file cluster-6381.conf
cluster-node-timeout 15000
EOF

mkdir /var/{lib,log,run}/redis -p
chown -R redis: /var/{lib,log,run}/redis /etc/redis*

cat > /usr/lib/systemd/system/redis-6381.service << EOF
[Unit]
Description=Redis 6381
After=network.target
After=network-online.target
Wants=network-online.target

[Service]
ExecStart=/usr/local/bin/redis-server /etc/redis-6381.conf --supervised systemd
ExecStop=/usr/local/bin/redis-cli -h `ifconfig eth0 | awk 'NR==2{print $2}'` -p 6381 shutdown
Type=notify
User=redis
Group=redis
RuntimeDirectory=redis
RuntimeDirectoryMode=0755

[Install]
WantedBy=multi-user.target
EOF

systemctl start redis-6381 && systemctl enable redis-6381
```

<br>

### 手动配置集群

#### 1.集群各节点互相发现

```bash
# 在一个节点上将各节点加入集群
redis-cli -h 10.0.0.5 -p 6380 cluster meet 10.0.0.5 6381
redis-cli -h 10.0.0.5 -p 6380 cluster meet 10.0.0.6 6380
redis-cli -h 10.0.0.5 -p 6380 cluster meet 10.0.0.6 6381
redis-cli -h 10.0.0.5 -p 6380 cluster meet 10.0.0.7 6380
redis-cli -h 10.0.0.5 -p 6380 cluster meet 10.0.0.7 6381

# 查看各节点是否配置成功
redis-cli -h 10.0.0.5 -p 6380 cluster nodes
```

<br>

#### 2.分配槽位

```bash
# 在三个节点平均分配 16384 个槽位
redis-cli -h 10.0.0.5 -p 6380 cluster addslots {0..5460}
redis-cli -h 10.0.0.6 -p 6380 cluster addslots {5461..10921‬}
redis-cli -h 10.0.0.7 -p 6380 cluster addslots {10922..16383}

# 查看集群槽位信息
redis-cli --cluster info 10.0.0.5 6380
```

<br>

#### 3.配置复制关系

```bash
# 查看集群各节点的 ID
redis-cli -h 10.0.0.5 -p 6380 cluster nodes | grep 6380

# 交叉配置复制关系 0:6381 = 1:6380 , 1:6381 = 2:6380 , 2:6381 = 0:6380
redis-cli -h 10.0.0.5 -p 6381 cluster replicate 32ff53a704b8b948a75378402205fe9aeec8bcfb
redis-cli -h 10.0.0.6 -p 6381 cluster replicate e216a3fa79465a45cbdfedd125836ca7edb759b8
redis-cli -h 10.0.0.7 -p 6381 cluster replicate e69b43cfc5f93aae9121e88fbf3467e6354df6fc

# 查看复制关系
redis-cli -h 10.0.0.5 -p 6380 cluster nodes
```

<br>

### 自动配置集群

```bash
redis-cli --cluster create 10.0.0.5:6380 10.0.0.6:6380 10.0.0.7:6380 10.0.0.5:6381 10.0.0.6:6381 10.0.0.7:6381 --cluster-replicas 1
yes
# 检查集群各节点
redis-cli -h 10.0.0.7 -p 6380 cluster nodes
```

<br>

```bash
# 验证集群的写入数据分配是否平衡
# 写入测试数据
for i in {1..10000}; do redis-cli -c -h 10.0.0.5 -p 6380 set k${i} v${i}; done

# 查看各节点 key 的数量
redis-cli --cluster info 10.0.0.5 6380

# 执行集群检查
redis-cli --cluster rebalance 10.0.0.5 6380
```

<br>

<br>

## 集群扩容和收缩

### 增配 2 个实例

```bash
cat > /etc/redis-6390.conf << EOF
# 以守护进程模式启动
daemonize yes
#绑定的主机地址
bind `ifconfig eth0 | awk 'NR==2{print $2}'`
# 监听端口
port 6390
# pid 文件和 log 文件地址
pidfile /var/run/redis/redis-6390.pid
logfile /var/log/redis/redis-6390.log
# 设置数据库的数量，默认数据库为 0
databases 16
# 指定本地持久化文件的文件名,默认是 dump.rdb
dbfilename redis-6390.rdb
# 本地数据库的目录,默认是 ./
dir /var/lib/redis
# AOF 重写
appendonly yes
appendfilename "redis-6390.aof"
appendfsync everysec
# 开启集群
cluster-enabled yes
cluster-config-file cluster-6390.conf
cluster-node-timeout 15000
EOF

mkdir /var/{lib,log,run}/redis -p
chown -R redis: /var/{lib,log,run}/redis /etc/redis*

cat > /usr/lib/systemd/system/redis-6390.service << EOF
[Unit]
Description=Redis 6390
After=network.target
After=network-online.target
Wants=network-online.target

[Service]
ExecStart=/usr/local/bin/redis-server /etc/redis-6390.conf --supervised systemd
ExecStop=/usr/local/bin/redis-cli -h `ifconfig eth0 | awk 'NR==2{print $2}'` -p 6390 shutdown
Type=notify
User=redis
Group=redis
RuntimeDirectory=redis
RuntimeDirectoryMode=0755

[Install]
WantedBy=multi-user.target
EOF

systemctl start redis-6390 && systemctl enable redis-6390
```

```bash
cat > /etc/redis-6391.conf << EOF
# 以守护进程模式启动
daemonize yes
#绑定的主机地址
bind `ifconfig eth0 | awk 'NR==2{print $2}'`
# 监听端口
port 6391
# pid 文件和 log 文件地址
pidfile /var/run/redis/redis-6391.pid
logfile /var/log/redis/redis-6391.log
# 设置数据库的数量，默认数据库为 0
databases 16
# 指定本地持久化文件的文件名,默认是 dump.rdb
dbfilename redis-6391.rdb
# 本地数据库的目录,默认是 ./
dir /var/lib/redis
# AOF 重写
appendonly yes
appendfilename "redis-6391.aof"
appendfsync everysec
# 开启集群
cluster-enabled yes
cluster-config-file cluster-6391.conf
cluster-node-timeout 15000
EOF

mkdir /var/{lib,log,run}/redis -p
chown -R redis: /var/{lib,log,run}/redis /etc/redis*

cat > /usr/lib/systemd/system/redis-6391.service << EOF
[Unit]
Description=Redis 6391
After=network.target
After=network-online.target
Wants=network-online.target

[Service]
ExecStart=/usr/local/bin/redis-server /etc/redis-6391.conf --supervised systemd
ExecStop=/usr/local/bin/redis-cli -h `ifconfig eth0 | awk 'NR==2{print $2}'` -p 6391 shutdown
Type=notify
User=redis
Group=redis
RuntimeDirectory=redis
RuntimeDirectoryMode=0755

[Install]
WantedBy=multi-user.target
EOF

systemctl start redis-6391 && systemctl enable redis-6391

redis-cli -h 10.0.0.5 -p 6380 cluster meet 10.0.0.5 6390
redis-cli -h 10.0.0.5 -p 6380 cluster meet 10.0.0.5 6391
```

<br>

### 使用工具扩容和收缩

```bash
# 扩容
# 重新分配槽位
redis-cli --cluster reshard 10.0.0.5:6380

# 第一次交互：需要迁移多少个槽位
How many slots do you want to move (from 1 to 16384)? 4096

# 第二次交互：接收槽位节点的 ID 是什么
What is the receiving node ID? 6390 ID

# 第三次交互：哪些节点需要迁出槽位 
Source node #1: all

# 第四次交互：确认是否执行
Do you want to proceed with the proposed reshard plan (yes/no)? yes

# 查看集群槽位状况
redis-cli --cluster info 10.0.0.5:6380

# 收缩
# 重新分配槽位
redis-cli --cluster reshard 10.0.0.5 6380

# 第一次交互：需要迁移多少个槽位
How many slots do you want to move (from 1 to 16384)? 1365

# 第二次交互：接收槽位节点的 ID 是什么
What is the receiving node ID? 6380 ID 

# 第三次交互：哪些节点需要迁出槽位
Source node #1: 6390 ID 
Source node #2: done

# 第四次交互：确认是否执行
Do you want to proceed with the proposed reshard plan (yes/no)? yes

# 重复上面的操作，直到 6390 所有的槽位都被分配出去

# 检查集群槽位状况
redis-cli --cluster info 10.0.0.5:6380

# 使用工具删除多余节点
redis-cli --cluster del-node 10.0.0.5:6390 6390 ID
redis-cli --cluster del-node 10.0.0.5:6391 6391 ID
```

<br>

<br>

## redis-cli--cluster 集群命令

```bash
Cluster Manager Commands:
  create         host1:port1 ... hostN:portN   # 创建集群
                 --cluster-replicas <arg>      # 从节点个数
  check          host:port                     # 检查集群
                 --cluster-search-multiple-owners # 检查是否有槽同时被分配给了多个节点
  info           host:port                     # 查看集群状态
  fix            host:port                     # 修复集群
                 --cluster-search-multiple-owners # 修复槽的重复分配问题
  reshard        host:port                     # 指定集群的任意一节点进行迁移 slot，重新分 slots
                 --cluster-from <arg>          # 需要从哪些源节点上迁移 slot，可从多个源节点完成迁移，以逗号隔开，传递的是节点的 node id，还可以直接传递 --from all，这样源节点就是集群的所有节点，不传递该参数的话，则会在迁移过程中提示用户输入
                 --cluster-to <arg>            # slot需要迁移的目的节点的 node id，目的节点只能填写一个，不传递该参数的话，则会在迁移过程中提示用户输入
                 --cluster-slots <arg>         # 需要迁移的 slot 数量，不传递该参数的话，则会在迁移过程中提示用户输入
                 --cluster-yes                 # 指定迁移时的确认输入
                 --cluster-timeout <arg>       # 设置 migrate 命令的超时时间
                 --cluster-pipeline <arg>      # 定义集群获取槽中的 key ,一次取出的 key 数量，不传的话使用默认值为 10
                 --cluster-replace             # 是否直接 replace 到目标节点
  rebalance      host:port					 # 指定集群的任意一节点进行平衡集群节点 slot 数量 
                 --cluster-weight <node1=w1...nodeN=wN>    # 指定集群节点的权重
                 --cluster-use-empty-masters    # 设置可以让没有分配 slot 的主节点参与，默认不允许
                 --cluster-timeout <arg>        # 设置超时时间
                 --cluster-simulate             # 模拟 rebalance 操作，不会真正执行迁移操作
                 --cluster-pipeline <arg>      # 定义 cluster 一次取出的 key 数量，默认值为 10
                 --cluster-threshold <arg>     # 迁移的 slot 阈值超过 threshold，执行 rebalance 操作
                 --cluster-replace             # 是否直接 replace 到目标节点
  add-node       new_host:new_port existing_host:existing_port  # 添加节点，把新节点加入到指定的集群，默认添加主节点
                 --cluster-slave              # 新节点作为从节点，默认随机一个主节点
                 --cluster-master-id <arg>    # 给新节点指定主节点
  del-node       host:port node_id            # 删除给定的一个节点，成功后关闭该节点服务
  call           host:port command arg arg .. arg    # 在集群的所有节点执行相关命令
  set-timeout    host:port milliseconds		# 设置 cluster-node-timeout
  import         host:port                   # 将外部 redis 数据导入集群
                 --cluster-from <arg>        # 将指定实例的数据导入到集群
                 --cluster-copy              # migrate 时指定 copy
                 --cluster-replace           # migrate 时指定 replace
```

<br>

```bash
# 使用工具自动部署 redis 集群
# 1.恢复集群初始化
redis-cli -h 10.0.0.5 -p 6380 FLUSHALL
redis-cli -h 10.0.0.6 -p 6380 FLUSHALL
redis-cli -h 10.0.0.7 -p 6380 FLUSHALL
redis-cli -h 10.0.0.5 -p 6381 FLUSHALL
redis-cli -h 10.0.0.6 -p 6381 FLUSHALL
redis-cli -h 10.0.0.7 -p 6381 FLUSHALL
redis-cli -h 10.0.0.5 -p 6380 CLUSTER RESET
redis-cli -h 10.0.0.6 -p 6380 CLUSTER RESET
redis-cli -h 10.0.0.7 -p 6380 CLUSTER RESET
redis-cli -h 10.0.0.5 -p 6381 CLUSTER RESET
redis-cli -h 10.0.0.6 -p 6381 CLUSTER RESET
redis-cli -h 10.0.0.7 -p 6381 CLUSTER RESET
redis-cli -h 10.0.0.5 -p 6380 CLUSTER NODES  

# 2.使用工具初始化
redis-cli --cluster create 10.0.0.5:6380 10.0.0.6:6380 10.0.0.7:6380 10.0.0.5:90 10.0.0.5:6381 10.0.0.6:6381 10.0.0.7:6381 10.0.0.5:91 --cluster-replicas 1

# 3.检查集群
redis-cli --cluster info 10.0.0.5 6380
redis-cli -h 10.0.0.5 -p 6380 CLUSTER NODES 
redis-cli --cluster check 10.0.0.5 6380
```

<br>

```bash
# 模拟故障
# 场景：迁移数据时人为中断了，导致槽的状态不对
[11213-<-a69e46ea7560684a7061ddb6dc3f854a1ef3dbd4] 51
[11213->-ccaa5dcb0f0320332100594d629122b2702660d5] 53

# 使用工具修复：
redis-cli --cluster fix 10.0.0.51:6380

# 手动修复：
CLUSTER SETSLOT <slot> STABLE

# 使用工具维护集群的好处
因为工具有很多判断条件，更加严谨，健壮性更好。
删除槽，使用工具会判断，如果槽里有数据，就不执行。
添加节点使用工具会判断，如果新增加的节点本身不为空，就不允许加入到集群。
删除节点使用工具会判断，如果本删除节点本身还有槽，就不允许删除。
```

<br>

## 数据迁移

```bash
# 1.5.0 版本直接可使用自带命令迁移(需要设置密码为空才能进行导入)
# 将单节点 10.0.0.7:6379 的数据迁移到 10.0.0.5:6380 节点的集群 
# 不加 copy 参数相当于 mv 命令，旧数据迁移成功后就会消失
redis-cli --cluster import 10.0.0.5:6380 --cluster-from 10.0.0.7:6379

# 添加 copy 参数相当于 cp 命令，旧数据迁移成功后会保留
redis-cli --cluster import 10.0.0.5:6380 --cluster-copy --cluster-from 10.0.0.7:6379 

# 添加 replace 参数会覆盖掉同名的数据，对新集群新增加的数据不受影响
redis-cli --cluster import 10.0.0.5:6380 --cluster-copy --cluster-replace --cluster-from  10.0.0.7:6379 

# 实验：导入一个持续有数据写入的节点
# 同时开 2 个终端，一个写入 key，一个执行导入命令
for i in {1..1000}; do redis-cli set k_${i} v_${i}; sleep 0.2;echo ${i}; done

redis-cli --cluster import 10.0.0.5:6380 --cluster-copy --cluster-replace --cluster-from  10.0.0.7:6379 

# 结论：只会导入当你执行导入命令那一刻时已有的数据，类似于快照，对于后面再写入的数据不会更新
```

<br>

## 分析 KEY 的大小

```bash
# 使用自带工具分析
redis-cli --bigkeys 

# 使用第三方工具分析
# 1.安装工具
yum install python-pip gcc python-devel -y
cd /opt/
git clone https://github.com/sripathikrishnan/redis-rdb-tools
cd redis-rdb-tools
pip install python-lzf
python setup.py install

# 2.生成测试数据
redis-cli -h 10.0.0.5 -p 6379 set test $(cat test.txt)

# 3.执行 bgsave 生成 rdb 文件
redis-cli -h 10.0.0.5 -p 6379 bgsave

# 4.使用工具分析
rdb -c memory /var/lib/redis/redis.rdb -f redis.rdb.csv

5.过滤分析
awk -F "," '{print $4,$3}' redis.rdb.csv | sort -r

6.将结果整理汇报给领导,询问开发这个 key 是否可以删除
```

<br>

## 内存管理

```bash
1.设置最大内存限制
config set maxmemory 2G

2.内存回收机制
生产上一定要限制 redis 的内存使用大小。
当达到内存使用限制之后 redis 会出发对应的控制策略
redis 支持 6 种策略：
1.noevicition       # 默认策略，不会删除任务数据，拒绝所有写入操作并返回客户端错误信息，此时只响应读操作
2.volatile-lru      # 根据 LRU 算法删除设置了超时属性的 key，直到留出足够空间为止，如果没有可删除的 key，则会退回到 noevicition 策略
3.allkeys-lru       # 根据 LRU 算法删除 key，不管数据有没有设置超时属性
4.allkeys-random    # 随机删除所有 key
5.volatile-random   # 随机删除过期 key
5.volatile-ttl      # 根据 key 的 ttl，删除最近要过期的 key

3.生产上 redis 限制多大内存
先预留系统一半内存
48G 总内存 
24G 系统 + 24G redis
redis 先给 8G 内存，内存不足后，分析结果通知相关人员，排查一下是否所有的 key 都必须保留?
redis 再给到 12G 内存，内存不足后，分析结果通知相关人员，排查一下是否所有的 key 都必须保留?
redis 再给到 16G 内存，内存不足后，分析结果通知相关人员，排查一下是否所有的 key 都必须保留?
等到 24G 都不够用时，汇报领导，考虑买内存了。
等到 35G 的时候，就要考虑加内存，还是扩容机器。
```

<br>

## 集群相关命令

```bash
# 以正则匹配的方式查看满足条件的 key
redis-cli --scan --pattern 'k*'

# 查看集群各节点状态
redis-cli -h 10.0.0.5 -p 6380 CLUSTER NODES

# 配置集群中的复制关系
redis-cli -h 10.0.0.5 -p 6381 cluster replicate 32ff53a704b8b948a75378402205fe9aeec8bcfb

# 添加槽位
redis-cli -h 10.0.0.5 -p 6380 cluster addslots {0..5460}

# 手动修复指定的槽位
CLUSTER SETSLOT <slot> STABLE

# 平衡集群中各个节点的 slot 数量
redis-cli --cluster rebalance 10.0.0.5 6380

# 删除集群节点
redis-cli --cluster del-node 10.0.0.5:6390 节点(6390)ID

# 修复集群
redis-cli --cluster fix 10.0.0.5:6380

# 清空集群信息
redis-cli -h 10.0.0.5 -p 6380 cluster reset

# 查看集群状态
redis-cli -h 10.0.0.5 -p 6380 cluster info

# 查看集群信息
redis-cli --cluster info 10.0.0.5 6380

# 执行集群检查
redis-cli --cluster check 10.0.0.5 6380

# 加入节点到集群
redis-cli -h 10.0.0.5 -p 6380 cluster meet 10.0.0.7 6381

# 重新分配槽位
redis-cli --cluster reshard 10.0.0.5:6380

# 主动发起集群角色切换
redis-cli -h 10.0.0.5 -p 6380 cluster failover
```
