---
date: 2013-11-21 12:12:11
title: capistrano使用
tags: deploy
category: 
  - tools
---

<strong>先简单介绍下Capistrano</strong>

Capistrano是一种在多台服务器上<a href="http://baike.baidu.com/view/513191.htm" target="_blank">运行脚本</a>的开源工具，它主要用于部署web应用。它自动完成多台服务器上新版本的同步更新，包括数据库的改变。Capistrano最初由Jamis Buck用Ruby开发，并用<a href="http://baike.baidu.com/view/1772268.htm" target="_blank">RubyGems</a>部署渠道部署。现在Capistrano不仅限于应用Ruby on Rails的 web应用框架，而且可以用于部署用其他框架的web应用程序，比如用PHP开发的。Capistran最初是用来应用于bash指令行。现在Ruby on Rails框架的用于也可以使用它的新特性，例如，对当前web应用部署改变使其更新版本，或者使其回滚到之前的旧版本。

<strong>如何使用</strong>

Capistrano最新版本是v3，但是v3暂时只支持git协议，如果想使用svn等其他协议可以用v2，所以下面主要是针对v2.

1.安装：
<p style="padding-left: 30px;">gem install capistrano（需要先安装ruby）</p>
<p style="padding-left: 30px;">装完后会有两个工具脚本加入环境变量</p>
<p style="padding-left: 30px;">1.cap      调用capistrano task</p>
<p style="padding-left: 30px;">2.capify  初始化目录</p>
<p style="padding-left: 30px;"><!--more--></p>
2.初始化部署脚本
<pre class="lang:sh decode:true" title="init capify">mkdir capProject
cd capProject
capify .</pre>


Capfile 部署脚本入口文件

config 配置目录

&nbsp;

deploy.rb配置

<a title="Configuration-Variables" href="https://github.com/capistrano/capistrano/wiki/2.x-Significant-Configuration-Variables" target="_blank">https://github.com/capistrano/capistrano/wiki/2.x-Significant-Configuration-Variables</a>

<strong>1.配置版本控制</strong>
使用git管理代码
set :scm, :git
使用svn管理代码
set :scm, :subversion

<strong>2.设置版本控制用户名密码</strong>

set :scm_username,'xx'
set :scm_password,'xx'

<strong>3.设置repository地址</strong>

set :repository, "https://xxxx.git"

<strong>4.设置web服务器地址 多个逗号分隔，注释掉:role app和:role db在php部署中不需要</strong>

role :web, "devhc.com"

多台服务器

role :web, "devhc.com","devhc1.com"

<strong>5.设置ssh密码（如果设置sshkey nopassword方式访问则不填写）</strong>

set :username,'xx'
set :password,'xx'

<strong>6.设置部署目标位置</strong>

set :deploy_to,"/var/www/#{application}"

这里是引用了application的名字，若application名称为blog，则部署位置为/var/www/blog

<strong>7.杂项设置（可选）</strong>

<span style="color: #ff0000;">非ruby项目该值都设为false，不然会提示public文件夹不存在</span>

set :normalize_asset_timestamps,false

设置需要共享的目录 默认是： %w(public/system log tmp/pids)

没有就设空
set :shared_children,%w()

如yii框架中assets和protected/runtime需要共享

set :shared_children, %w(assets protected/runtime protected/config)

保持最新release个数

set :keep_releases,5

&nbsp;

设置current_path，即当前版本目录，其实是一个软连接，执行release中最新版本代码,如果是apache为服务器，则为其web目录,默认则为

deploy_path/current,如果使用默认的话需要改下apache配置指向这里

set current_path,'/var/www/xxx'

&nbsp;

实例配置如下：
<pre class="lang:ruby decode:true" title="capistrano deploy.rb">set :application, "xxx"

set :repository, "https://xxxx.git"

set :normalize_asset_timestamps,false
set :deploy_to,"/var/www/#{application}"
set :scm,:git
set :username,'xx'
set :password,'xx'
set :use_sudo,true

set :keep_releases,5
set :shared_children, %w(assets protected/runtime)

role :web, "devhc.com"</pre>
&nbsp;

部署脚本使用

cap deploy:setup

初始化目录 release，shared目录

&nbsp;

cap deploy 执行部署

&nbsp;

多环境部署

通常项目部署有sandbox,ready,production等环境，分别一一设置部署比较麻烦，通过capistrano就很方便解决这个问题。

1.使用Multistage Extension扩展

通过gem安装<strong>capistrano-ext</strong>扩展

mkdir config/deploy 新建stages配置目录
<pre class="wrap:true lang:ruby decode:true" title="cap multi stage">#deploy.rb中引入扩展和配置stages
set :stages, %w(production sandbox)
set :default_stage, "sandbox"
require 'capistrano/ext/multistage'</pre>
#config/deploy/production.rb
<pre class="wrap:true lang:ruby decode:true" title="cap multi stage2">role :web, "devhc.com"
set :deploy_to, "/var/www/#{application}-production/"</pre>
#config/deploy/sandbox.rb
<pre class="wrap:true lang:ruby decode:true" title="cap multi stage3">role :web, "sandbox.devhc.com"
set :deploy_to, "/var/www/#{application}-sandbox/"</pre>
cap sandbox deploy #部署沙盒
cap production deploy #部署线上

2. 不使用Multistage Extension实现多环境部署
<pre class="wrap:true lang:ruby decode:true" title="without multi deploy">set :application, 'example-website'

task :production do
  role :web, "devhc.com"
  set :deploy_to, "/var/www/#{application}-production/"
  set :deploy_via, :remote_cache
end

task :sandbox do
  role :web, "localhost"
  set :deploy_to, "/var/www/#{application}-sandbox/"
  set :deploy_via, :copy
end</pre>
&nbsp;