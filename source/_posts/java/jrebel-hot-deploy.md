---
layout: post
title: 使用JRebel 对java 应用热部署开发
date: 2018-05-30 12:01:12
tags:
    - java
    - hotdeploy
category: 
    - java
    - tools
---
# 使用JRebel 对java 应用热部署开发

1. Java 热更新
Java 开发Web Server 相关经常被人吐槽修改代码需要重启服务器，非常不方便,JRebel 是一个非常方便的热更新部署插件.

## JRebel安装
1. 下载jrebel:https://zeroturnaround.com/software/jrebel/download/#!/have-license/intellij
1. unzip -d /opt jrebel-xxxx-nosetup.zip (xxxx 替换下载版本)
1. java -jar /opt/jrebel/jrebel.jar -set-remote-password 12345678
<!--more-->

## Jrebel 激活
1. Activate Code:
    1. 通过Facebook 登录 https://my.jrebel.com/ 获取
1. LisenceServer(By iLanyu) 
    1. docker run -d -p 8888:8888 ilanyu/golang-reverseproxy

## IntelJ Idea
1. 安装插件并激活插件
    1. jntelj 安装jrebel 插件:File-Setting-Plugins 安装Jrebel(安装慢可以设置代理下载)
    1. 激活Help->Jrebel-Activation->LisenceServer
    1. http://localhost:8888/uuid-your-gen (uuid 可以https://www.uuidgenerator.net/ 生成)
1. 配置JRebel插件
    1. 添加File-Setting-Jrebel-RemoteServer,添加remote server
    1. 点击+,name:model-server,url:http://localhost:8000
    1. Server Auth:填写12345678
    1. 打开Views -> TopWindow ->Jrebel 视图,勾上热部署模块
    1. 在启动脚本加上参数
        1. 类似 java -agentpath:/opt/jrebel/lib/libjrebel64.so -Drebel.remoting_plugin=true -Drebel.remoting_port=8000 foo.bar.MyApp
    1. 启动Server,在项目修改代码，编译后会自动加载
1. 配置保存自动编译加载 Auto Compile
    1. 默认代码保存不会自动加载需要右键编译或者快捷键(Ctrl/Cmd+Shift+F9)
    1. 开启保存编译[IntelJ Auto Compile](https://stackoverflow.com/questions/12744303/intellij-idea-java-classes-not-auto-compiling-on-save)

