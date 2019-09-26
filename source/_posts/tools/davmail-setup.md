---
layout: post
title: Linux 使用davmail thunderbird 连接 outlook exchange 
date: 2018-05-30 14:01:12
tags:
    - linux
    - tools
category:
    - linux
    - tools
---

## davmail 配置
[官网](http://davmail.sourceforge.net/)
DavMail 是一个Java 编写Exchange 的代理gateway软件,让一般smtp+imap/pop3 客户端可以通过这个代理收发邮件

<!--more-->
## davmail 安装
[davmail download](https://sourceforge.net/projects/davmail/files/davmail/4.8.6/davmail-4.8.6-2600.zip/download)
1. unzip -d davmailxxx.zip -d /opt/davmail

## davmail 配置
1. 创建/opt/davmail/davmail.properties 拷贝官方实例配置 [Official Config](http://davmail.sourceforge.net/serversetup.html)
1. 如果是服务器注意修改davmail.url 为内部服务器地址
1. 修改davmail.logFilePath=/opt/davmail/davmail.log
1. 将davmail 注册到本地服务
    1. useradd davmail
    1. sudo chown -R davmail:davmail /opt/davmail
    1. sudo vim /lib/systemd/system/davmail.service
    ```
    [Unit]
    Description=davmail proxy for exchange
    [Service]
    User=davmail
    Group=davmail
    Type=simple
    ExecStart=/opt/davmail/davmail.sh /opt/davmail/davmail.properties
    PIDFile=/var/run/davmail.pid
    [Install]
    WantedBy=multi-user.target graphical.target
    ```
    1. systemctl enable davmail
    1. systemctl start davmail
    1. ps aux|grep davmail 检查服务是否正常 可以通过直接运行 /opt/davmail/davmail.sh /opt/davmail/davmail.properties 查看错误信息
1. 修改/opt/davmail/davmail.sh
```
#!/bin/bash
BASE=`dirname $0`
BASE=/opt/davmail
for i in $BASE/lib/*; do export CLASSPATH=$CLASSPATH:$i; done
JAVA=`which java`
$JAVA -Xmx512M -Dsun.net.inetaddr.ttl=60 -cp $BASE/davmail.jar:$CLASSPATH davmail.DavGateway $1
```


## Thunder bird 配置
1. STMP 
    1. Server:localhost Port:1025
    1. UserName: you namee
    1. Connection security:Non
    1. Authentication Method:Password ,transmitted insecureity
1. IMAP:
    1. Server:localhost Port:1143
    1. UserName: you name
    1. Connection security:None
    1. Authentication Method:Password ,transmitted insecureity
