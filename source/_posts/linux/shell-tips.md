---
date: 2014-08-26
title: Shell 实用Tips
tags: shell
category: linux
---

1. 获取script所在目录
```bash
    JOB_PATH=$(cd "$(dirname "$0")"; pwd)
 ```
2. 根据名称kill script
```bash
     ps aux|grep $name|grep -v grep|awk '{print $2}'|xargs kill -s 9
 ```
3. 根据参数获取当前日期，默认当天
```bash 
if [ -z "$1" ]; then
    date=`date +"%Y-%m-%d"`;
else
    date=`date -d "$1" +"%Y-%m-%d"`;
fi
```
4. 获取其他script输出赋给变量
```bash
a=`php -r "echo 1;"`
echo $a
```
5. 获取内存占用前10进程
```
bash ps aux|sort -k4r|head -n 10
```
6. 查看一个目录下总文件大小和
```
bash du -sh .
```
7. 环境变量设置

* 系统 /etc/profile /etc/bashrc
* 个人 ~/.bash_profile ~/.bashrc
* 推荐：/etc/profile.d/
* 比如新增hive加入path
* 建立一个/etc/profile.d/hive.sh

```bash
export HIVE_HOME=/opt/hive
PATH=$HIVE_HOME/bin:$PATH
```


8. shell实用快捷键

* Ctrl + a 光标切换到行首（经常用）
* Ctrl + e 光标切换到行尾
* cd - 切换到上次目录
* !! 上次命令（一般使用 针对忘记打sudo，下面打sudo !!即可）
* Ctrl ＋l 清屏
* Ctrl + f / Ctrl +b 等同左右箭頭
* Ctrl + k 剪切光標后
* Ctrl + u 剪切光標前
* Ctrl + y 粘贴shell剪切板 （注意这三个命令和系统剪切板不共享）


9. 统计目录各文件夹大小 
```bash
for dir in `ls`;
    do du -sh $dir;
done
```


10. 搜索一个目录下文件 含有关键字 keyword的文件
```bash
find . -type f |xargs grep keyword
```