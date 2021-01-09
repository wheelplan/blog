---
title: Zabbix 监控
date: 2018-02-13 22:34:33
top_img: https://5b0988e595225.cdn.sohucs.com/images/20180731/d8cd390c882e44cd822c1a72d0fe3709.png
keywords: zabbix 部署 微信报警 邮件报警
tags:
 - zabbix
---

# Zabbix 监控

Zabbix 是一个企业级的分布式开源监控方案，可以监控服务器健康性以及网络参数的一款软件，Zabbix 几乎可以为任何时间配置邮件警告，这样用户可以实时通过邮箱接收服务器所发生的任何问题。对于已经存在的数据，Zabbix 也提供了出色的报告和可视化分析工具。zabbix server 可以通过 [SNMP](https://baike.baidu.com/item/SNMP)，zabbix agent，[ping](https://baike.baidu.com/item/ping/6235)，端口监视等方法提供对远程服务器 / 网络状态的监视，[数据收集](https://baike.baidu.com/item/数据收集/2245693) 等功能，它可以运行在Linux，Solaris，HP-UX，AIX，Free BSD，Open BSD，OS X等平台上。

Zabbix 支持主动轮询和被动捕获，它所有的报告，统计信息和配置参数都是通过 web 前端的方式进行访问。

Zabbix 是基于 GPL 通用许可证编写和发行的，意味着它的源代码都是免费发行的，公众可以任意使用。

**Zabbix 的功能点**

Zabbix 是一个高度集成的网络监控解决方案，它提供了多种功能：

1.数据收集

- 可用性和功能性检查
- 支持 SNMP（包括主动轮训和被动获取），IPMI，JMX，VMware 监控
- 自定义检查
- 按照自定义的间隔收集需要的数据
- 通过 server / proxy + agents 来执行

2.灵活的阈值定义

- 可以非常灵活的设置阈值，也就是触发器，触发器从后端数据库获取参考值

3.高度可配置的告警

- 可根据递增机制，接收方和媒介类型自定义发送告警通知
- 使用宏变量可以使告警通知更加高效有用
- 自动相应动作可包含远程[命令](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.linuxcool.com%2F)

4.实施绘制图表

- 使用内置表绘制功能可以将监控项的内容实时绘制图表

5. WEB 监控功能

- Zabbix 可以追踪模拟鼠标在 web 上的点击操作，可以用来检测web的功能和响应时间

6.丰富的可视化选项

- 支持创建自定义的图表，一个试图集中展现多个监控项
- 网络拓扑图
- 以仪表盘的样式自定义大屏展现和幻灯片轮询播放
- 报表
- 监控内容的高级（业务）视图

7.历史数据存储

- 数据库数据
- 可配置历史数据
- 内置数据管理机制（housekeeping）

8.配置简单

- 将被监控对象添加为主机
- 在数据库中获取主机进行监视
- 应用模板来监控设备

9.使用模板

- 在模板中分组检查
- 模板可以关联其他模板

10.网络发现

- 自动发现网络设备
- 监控代理自动注册
- 发现文件系统，网络接口和 SNMP OID 值

11.快捷的 web 界面

- PHP Web 前端
- 可从任何地方访问
- 你可以定制自己的操作方式
- 审核日志

12.Zabbix Api

- Zabbix API 为 Zabbix 提供了对外的可编程接口，用于批量操作，第三方软件集成和其他目的

13.权限管理系统

- 安全用户认证
- 特定用户可以限制访问特定的视图

14.功能强大易于拓展的 agent

- 部署在被监控对象上
- 支持 Linux 和 Windows

15.二进制代码

- 为了性能和更少内存的占用，用C语言编写
- 便于移植

16.为复杂环境准备

- 使用 Zabbix proxy 代理服务器，使得远程监控更简单



# 部署 Zabbix 服务



官方安装手册  https://www.zabbix.com/documentation/4.0/zh/manual/installation



### 服务端

```bash
# 配置 yum 源
cd /tmp
wget https://mirrors.tuna.tsinghua.edu.cn/zabbix/zabbix/4.0/rhel/7/x86_64/zabbix-release-4.0-2.el7.noarch.rpm
yum install zabbix-release-4.0-2.el7.noarch.rpm -y

# 将源地址代换为清华源
sed -i 's#http://repo.zabbix.com#https://mirrors.tuna.tsinghua.edu.cn/zabbix#g' /etc/yum.repos.d/zabbix.repo

# 安装 zabbix 服务端、前端和数据库
yum install -y zabbix-server-mysql zabbix-web-mysql mariadb-server

# 启动数据库并设置为开机自启
systemctl start mariadb && systemctl enable mariadb

# 初始化数据库
mysql_secure_installation


# 创建 zabbix 数据库
mysql -e 'create database zabbix character set utf8 collate utf8_bin;'

# 创建 zabbix 用户并授权
mysql -e "grant all privileges on zabbix.* to 'zabbix'@'localhost' identified by 'linux';"

# 导入数据
zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -plinux zabbix


# 修改 zabbix-server 配置文件连接数据库
sed -i '/DBPassword=/c DBPassword=linux' /etc/zabbix/zabbix_server.conf

# 修改 zabbix-web 前端配置的时区
sed -i "/date.timezone/c php_value date.timezone Asia/Shanghai" /etc/httpd/conf.d/zabbix.conf
sed -i '/date.timezone/s#^#\t#' /etc/httpd/conf.d/zabbix.conf

#启动 zabbix-server 服务并设置为开机自启
systemctl start zabbix-server && systemctl enable zabbix-server

# 启动 httpd 服务并设置为开机自启
systemctl start httpd && systemctl enable httpd
```



### 客户端

```bash
# 安装 zabbix-agent
cd /tmp
wget https://mirrors.tuna.tsinghua.edu.cn/zabbix/zabbix/4.0/rhel/7/x86_64/zabbix-agent-4.0.18-1.el7.x86_64.rpm
yum install -y zabbix-agent-4.0.18-1.el7.x86_64.rpm

# 修改客户端配置文件关联服务端
egrep -v '^$|#' /etc/zabbix/zabbix_agentd.conf
PidFile=/var/run/zabbix/zabbix_agentd.pid
LogFile=/var/log/zabbix/zabbix_agentd.log
LogFileSize=0
Server=10.0.0.3				# Zabbix 服务端 IP
ServerActive=10.0.0.3	    # Zabbix 服务端 IP(自动注册到服务端)
Hostname=10.0.0.3			# Zabbix 客户端的昵称(自定义)
HostMetadata=linux			# 主机元数据 (自动注册匹配的内容)
Include=/etc/zabbix/zabbix_agentd.d/*.conf

# 启动 zabbix-agent 并设置为开机自启
systemctl start zabbix-agent && systemctl enable zabbix-agent
```



### Web 端

⦁	配置 >> 动作 >> 自动注册

![alt]("Zabbix 监控.assets/image-20200307114320692.png")

⦁	名称 >> 主机元数据

![image-20200307115237010](Zabbix 监控.assets/image-20200307115237010.png)

⦁	操作 >> 新的

![image-20200307115447979](Zabbix 监控.assets/image-20200307115447979.png)

⦁	与模板关联

![image-20200307115818109](Zabbix 监控.assets/image-20200307115818109.png)

⦁	添加到主机群组

![image-20200307120050342](Zabbix 监控.assets/image-20200307120050342.png)

客户端自动注册配置完成



# Zabbix 邮件报警



⦁	定义发件人

![](Zabbix 监控.assets/image-20200307173432729.png)

⦁	定义收件人

![image-20200307180013340](Zabbix 监控.assets/image-20200307180013340.png)

⦁	配置动作

![image-20200307180256028](Zabbix 监控.assets/image-20200307180256028.png)

⦁	自定义报警参考

![image-20200307180746241](Zabbix 监控.assets/image-20200307180746241.png)

默认标题：故障{TRIGGER.STATUS},服务器:{HOSTNAME1}发生: {TRIGGER.NAME}故障!

告警主机:{HOSTNAME1}

告警时间:{EVENT.DATE} {EVENT.TIME}

告警等级:{TRIGGER.SEVERITY}

告警信息: {TRIGGER.NAME}

告警项目:{TRIGGER.KEY1}

问题详情:{ITEM.NAME}:{ITEM.VALUE}

当前状态:{TRIGGER.STATUS}:{ITEM.VALUE1}

事件ID:{EVENT.ID}



![image-20200307181044345](Zabbix 监控.assets/image-20200307181044345.png)

恢复标题：恢复{TRIGGER.STATUS}, 服务器:{HOSTNAME1}: {TRIGGER.NAME}已恢复!

告警主机:{HOSTNAME1}

告警时间:{EVENT.DATE} {EVENT.TIME}

告警等级:{TRIGGER.SEVERITY}

告警信息: {TRIGGER.NAME}

告警项目:{TRIGGER.KEY1}

问题详情:{ITEM.NAME}:{ITEM.VALUE}

当前状态:{TRIGGER.STATUS}:{ITEM.VALUE1}

事件ID:{EVENT.ID}



⦁	启用触发器动作

![](Zabbix 监控.assets/image-20200307175233994.png)

完成



# Zabbix 微信报警



⦁	登陆企业微信公众号添加账户
https://work.weixin.qq.com/wework_admin/loginpage_wx 

⦁	记录企业 ID (corpid)

![image-20200307190643135](Zabbix 监控.assets/image-20200307190643135.png)

⦁	添加部门和成员

![image-20200307192312619](Zabbix 监控.assets/image-20200307192312619.png)

......

⦁	创建应用

![image-20200307191118689](Zabbix 监控.assets/image-20200307191118689.png)

![image-20200307191514606](Zabbix 监控.assets/image-20200307191514606.png)

⦁	记录 AgentId 和 Secret (appsecret)

![image-20200307192638166](Zabbix 监控.assets/image-20200307192638166.png)

⦁	记录要发送的企业微信部门 ID

![image-20200307204138691](Zabbix 监控.assets/image-20200307204138691.png)



⦁	查看配置文件里的脚本目录路径

```bash
grep "^AlertScriptsPath" /etc/zabbix/zabbix_server.conf
AlertScriptsPath=/usr/lib/zabbix/alertscripts
```



⦁	编写脚本

corpid=wwd327d0ea50c0dae0

appsecret=OxvRDNkcJdMuRZycMZmzKgNS-4jTdaYac4aeAxj9Fic

agentid=1000006

toparty=2

```bash
cat > /usr/lib/zabbix/alertscripts/wechat_alarm.py << EOF
#!/usr/bin/env python

import requests
import sys
import os
import json
import logging

logging.basicConfig(level = logging.DEBUG, format = '%(asctime)s, %(filename)s, %(levelname)s, %(message)s',
                datefmt = '%a, %d %b %Y %H:%M:%S',
                filename = os.path.join('/tmp','wechat_alarm.log'),
                filemode = 'a')
corpid='wwd327d0ea50c0dae0'
appsecret='OxvRDNkcJdMuRZycMZmzKgNS-4jTdaYac4aeAxj9Fic'
agentid=1000006

token_url='https://qyapi.weixin.qq.com/cgi-bin/gettoken?corpid=' + corpid + '&corpsecret=' + appsecret
req=requests.get(token_url)
accesstoken=req.json()['access_token']

msgsend_url='https://qyapi.weixin.qq.com/cgi-bin/message/send?access_token=' + accesstoken

#touser=sys.argv[1]
subject=sys.argv[2]
toparty='2|3'
message=sys.argv[2] + "\n\n" +sys.argv[3]

params={
      #  "touser": touser,
       "toparty": toparty,


    "msgtype": "text",
    "agentid": agentid,
    "text": {
            "content": message
    },
    "safe":0
}

req=requests.post(msgsend_url, data=json.dumps(params))

logging.info('sendto:' + toparty + ';;subject:' + subject + ';;message:' + message)
EOF

chown -R zabbix: /usr/lib/zabbix/alertscripts
chmod 744 /usr/lib/zabbix/alertscripts/wechat_alarm.py

yum install -y python2-pip
pip install requests
```



⦁	Web 端创建报警媒介

![image-20200307193253605](Zabbix 监控.assets/image-20200307193253605.png)

![image-20200307211911911](Zabbix 监控.assets/image-20200307211911911.png)

{ALERT.SENDTO}	  # 发送给谁，该参数在邮件告警中有作用，但微信告警中没有

{ALERT.SUBJECT}	 # 告警标题，该参数在邮件告警中有作用，但微信告警中没有

{ALERT.MESSAGE}	# 告警内容，在微信告警中有用



⦁	配置收件人

![image-20200307212810594](Zabbix 监控.assets/image-20200307212810594.png)

⦁	启用动作

![image-20200325222010660](Zabbix 监控.assets/image-20200325222010660.png)

⦁	针对个人的脚本

corpid='wwd327d0ea50c0dae0'

appsecret='OxvRDNkcJdMuRZycMZmzKgNS-4jTdaYac4aeAxj9Fic'

agentid=1000006

```bash
cat > /usr/lib/zabbix/alertscripts/wechat_alarm.py << EOF
#!/usr/bin/env python

import requests
import sys
import os
import json
import logging

logging.basicConfig(level = logging.DEBUG, format = '%(asctime)s, %(filename)s, %(levelname)s, %(message)s',
                datefmt = '%a, %d %b %Y %H:%M:%S',
                filename = os.path.join('/tmp','wechat_alarm.log'),
                filemode = 'a')
corpid='wwd327d0ea50c0dae0'
appsecret='OxvRDNkcJdMuRZycMZmzKgNS-4jTdaYac4aeAxj9Fic'
agentid=1000006

token_url='https://qyapi.weixin.qq.com/cgi-bin/gettoken?corpid=' + corpid + '&corpsecret=' + appsecret
req=requests.get(token_url)
accesstoken=req.json()['access_token']

msgsend_url='https://qyapi.weixin.qq.com/cgi-bin/message/send?access_token=' + accesstoken

touser=sys.argv[1]
subject=sys.argv[2]
# toparty='3|4|5|6'
message=sys.argv[2] + "\n\n" +sys.argv[3]

params={
        "touser": touser,
      # "toparty": toparty,


    "msgtype": "text",
    "agentid": agentid,
    "text": {
            "content": message
    },
    "safe":0
}

req=requests.post(msgsend_url, data=json.dumps(params))

logging.info('sendto:' + touser + ';;subject:' + subject + ';;message:' + message)
EOF
```



⦁	随机发送到指定用户玩笑脚本

```bash
#!/bin/bash 
num=$(echo $(($RANDOM%28+1)))
name=$(sed -n "${num}p" name.txt)
ok_boy=$(grep -v "${name}" name.txt)

for ok in ${ok_boy}
do
  python  wechat.py ${ok}  "$1"  "$2"
done
```
