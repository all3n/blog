---
layout: post
title: ssh 跳板机,Socket 代理
date: 2018-07-15 15:11:55
tags:
    - linux
    - ssh
category:
    - linux
    - ssh
---

## SSH Agent forwarding
ssh -A user@host
需要先
ssh-add ~/.ssh/id_rsa

或者修改 ~/.ssh/config
```
Host *
    ForwardAgent yes
    ForwardX11 yes
```
登录之后目标服务器 env
会有SSH_AUTH_SOCK 变量记录与本地key通信的socket

<!--more-->
## SSH ProxyCommand 跳板机

```
mkdir -p ~/.ssh/sockets/
ssh-add ~/.ssh/id_rsa
ssh-copy-id 跳板机用户@跳板机服务器
```

配置~/.ssh/config
```
Host *.xxx.com
  user YOU_SSH_USER
  ForwardAgent yes
  ControlMaster auto
  ControlPath ~/.ssh/sockets/%r@%h-%p
  ControlPersist 600
  ProxyCommand ssh -q -W %h:%p 跳板机用户@跳板机地址
```

## SSH Tunnel Socket5 代理 网页代理

ssh -fTND LOCAL_PROXY_PORT 代理服务器用户@代理服务器地址

然后 SwitchyOmega 配置 LOCAL_PROXY_PORT socket5 代理服务

就可以将web 流量通过socket5 转向代理服务器

## SSH 跳板访问mysql等其他服务

比如mysql 服务器是 1.2.3.4:3306 本地不能 访问
但是代理服务器可以访问

ssh -fN -L3306:1.2.3.4:3306  代理服务器用户@代理服务器地址

查看端口情况: lsof -i:3306

host 要写127.0.0.1
mysql -P3306 -h127.0.0.1 -uusername -ppassword
