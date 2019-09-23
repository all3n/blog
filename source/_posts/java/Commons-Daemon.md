---
author: wanghch
date: 2015-05-07 11:12:00
title: 使用 commons-daemon 管理daemon 服务
tags: 
category: java
---

1. 简单介绍一下 Commons Daemon

可将一个普通的 Java 应用变成系统的一个后台服务。

&nbsp;

2. JSVC

Jsvc is a set of libraries and applications for making Java applications run on UNIX more easily.
Jsvc allows the application (e.g. Tomcat) to perform some privileged operations as root (e.g. bind to a port &lt; 1024), and then switch identity to a non-privileged user.

安装：

介绍地址：http://commons.apache.org/proper/commons-daemon/jsvc.html
<pre class="lang:sh decode:true">wget http://apache.01link.hk//commons/daemon/source/commons-daemon-1.0.15-src.zip

unzip commons-daemon-1.0.15-src.zip

cd commons-daemon-1.0.15-src/src/native/unix
./configure --with-java=/usr/lib/jvm/java

make

sudo cp jsvc /usr/bin/</pre>
&nbsp;

3. java server

如果是maven 项目

添加
<pre class="lang:xhtml decode:true">&lt;dependency&gt;
&lt;groupId&gt;commons-daemon&lt;/groupId&gt;
&lt;artifactId&gt;commons-daemon&lt;/artifactId&gt;
&lt;version&gt;1.0.15&lt;/version&gt;
&lt;/dependency&gt;</pre>
创建server Daemon 入口

入口
<pre class="lang:java decode:true">public class Launcher implements Daemon {
	private Logger logger = LoggerFactory.getLogger("Launcher");

	@Override
	public void init(DaemonContext context) throws DaemonInitException,
			Exception {
		logger.info("init");
	}

	@Override
	public void start() throws Exception {
		logger.info("start");
		//....start your server
	}

	@Override
	public void stop() throws Exception {
                //.... stop your server
		logger.info("stop");
	}

	@Override
	public void destroy() {
		logger.info("destroy");

	}
}</pre>
4.start your daemon server

/usr/bin/jsvc -debug jar-with-deps.jar Laucher

-debug 帮助调试查看 实际可去掉

&nbsp;

&nbsp;

5.add service

sudo vim /etc/init.d/xxx
<pre class="lang:default decode:true">#!/bin/sh
#
# xxxx-server
#
# chkconfig: 2345 90 60
# description: xxxx

JOB_PATH=/data/deploy/xxx
EXEC=/usr/bin/jsvc
CLASS_PATH=$JOB_PATH/xxx-server-0.0.1-SNAPSHOT.jar
#LOG_OUT=$JOB_PATH/logs/job.log
LOG_OUT=/dev/null
#LOG_ERR=$JOB_PATH/logs/error.log
LOG_ERR=/dev/null
PID_FILE=$JOB_PATH/pid/server.pid
#DEBUG="-debug"
MAIN_CLASS=com.xx.MainClass

do_exec(){
        $EXEC \
        -cp $CLASS_PATH \
        -outfile $LOG_OUT \
        -home /usr/lib/jvm/jre-1.7.0-openjdk.x86_64/ \
        -errfile $LOG_ERR \
        -pidfile $PID_FILE \
        $1 \
        $DEBUG \
        $MAIN_CLASS
}
case "$1" in
        start)
                do_exec
                ;;
        stop)
                do_exec "-stop"
                ;;
        restart)
                do_exec "-stop"
                do_exec
                ;;
        *)
                echo "usage: {start|stop|restart}" &gt;&amp;2
                exit 3
                ;;
esac</pre>
&nbsp;

chmod +x /etc/init.d/xxx

启动服务

service xxx start

关闭服务

service xxx stop

重启服务

service xxx restart

&nbsp;

添加到系统启动运行

注意
<pre># chkconfig: 2345 90 60
# description: xxxx</pre>
这个是必须要有的 不然会报 service xxx does not support chkconfig

xxx服务必须在运行级2，3，4，5下被启动或关闭，启动的优先级是90，关闭的优先级是60, 对于运行级说明如下

0：关机

1：单用户模式

2：无网络支持的多用户模式

3：有网络支持的多用户模式

4：保留，未使用

5：有网络支持有X-Window支持的多用户模式

6：重新引导系统，即重启

&nbsp;

chkconfig --add xxx

就 ok 了  这样服务就可以开机启动

&nbsp;

chkconfig --list 可以看到 xxx

&nbsp;

&nbsp;