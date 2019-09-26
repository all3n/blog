---
author: wanghch
date: 2014-11-17 14:11:12
title: 使用mutt + msmtp 配置 发送邮件
tags: mail
category: 
    - linux
    - tools
---

msmtp + mutt 是一个非常好用的命令行发邮件工具

mutt 相当于 命令行 命令行 邮件客户端端 真正发送邮件由msmtp发送

1. msmtp

官方地址：http://sourceforge.jp/projects/sfnet_msmtp/
<pre class="lang:sh decode:true">wget http://nchc.dl.sourceforge.net/sourceforge/msmtp/msmtp-1.4.17.tar.bz2 
tar -jxvf msmtp-1.4.17.tar.bz2 
cd msmtp-1.4.17 
./configure --prefix=/usr/local/msmtp 
make 
make install 
cd /usr/local/msmtp/ 
mkdir etc 
cd etc</pre>
vim msmtprc 根据smtp 邮箱 配置
<pre class="lang:default decode:true">account default
host smtp.xxxx.com
port 465  
timeout 30
auth on
user xxx
password  xxxx
logfile /var/log/msmtp.log
auto_from off
from xxx@xxx.com
tls on
tls_starttls off
tls_certcheck off</pre>
echo "test" | /usr/local/msmtp/bin/msmtp youemail@xxxx.com

测试msmtp  配置 是否正常

&nbsp;

安装mutt

yum install mutt

vim /etc/Muttrc 其中修改几个地方

&nbsp;

set sendmail="/usr/local/msmtp/bin/msmtp"

set from="xxx@xxx.com"

这个可以是和 msmtp配置的不一样的

set realname="xxx mail"

可以给from 起名称

set editor="vim"

set charset="utf-8"

set rfc2047_parameters=yes

&nbsp;

发送带主题和附件的

echo "test mutt"|mutt -s "test subject 和中文 " xxx@xxx.com -a ~attach file

如果你发送的内容在文件中可以这样

mutt -s "test subject 和中文 " xxx@xxx.com -a ~attach file &lt; mail_content_file

&nbsp;