---
layout: post
title: Centos下一键搭建LNMP环境
category: linux
description: Centos下一键搭建LNMP环境，让你更快的部署服务器。
tags: linux centos lnmp环境
---

* content
{:toc}

![centos](http://7xj4mc.com1.z0.glb.clouddn.com/centos_logo.png)

## 描述

centos_lnmp_setup-1.0是针对Centos部署php+nginx+mysql+redis的一键安装包，包括软件：**php-5.5.25**，**nginx-1.7.2**，**mysql-5.6.24**，**redis-3.0.4**。后续升级工作还在紧锣密鼓的进行中...

<!--more-->

该安装包暂不支持切换软件版本。

## 下载安装脚本

{% highlight sh %}
git clone https://github.com/zer0131/centos_lnmp_setup.git
{% endhighlight %}

## 全部安装

修改权限，并安装

{% highlight sh %}
chmod -R 777 centos_lnmp_setup
cd centos_lnmp_setup
./install.sh
{% endhighlight %}

接下来就是耐心等待安装了

## 单独安装

目录中包含各个软件的安装脚本，如果你想单独安装软件，可以单独执行对应的.sh文件。
{% highlight sh %}
├─centos_lnmp_setup
│  ├─nginx_config          nginx配置目录
│  │  ├─nginx.conf         nginx配置文件
│  │  ├─fastcgi.conf       fastcgi配置文件
│  │  ├─vhosts             nginx虚拟目录
│  ├─install.sh            一键安装脚本
│  ├─install_env.sh        安装环境脚本
│  ├─install_mysql.sh      mysql安装脚本
│  ├─install_nginx.sh      nginx安装脚本
│  ├─install_php.sh        php安装脚本
│  └─install_redis.sh      redis安装脚本
{% endhighlight %}

**注意：单独安装任何软件都要先执行install_env.sh(执行一次就可以)**

## 后续操作

mysql设置密码：
{% highlight sh %}
mysqladmin -u root password 'u password'
{% endhighlight %}

开启或关闭mysql服务：
{% highlight sh %}
/etc/init.d/mysqld [start|stop]
{% endhighlight %}

开启或关闭nginx服务：
{% highlight sh %}
/etc/init.d/nginx [start|stop]
{% endhighlight %}

开启或关闭php-fmp服务：
{% highlight sh %}
/etc/init.d/php-fpm [start|stop]
{% endhighlight %}

nginx配置文件及虚拟目录在**/usr/local/nginx/conf/**下

**说明：所有的软件均安装在/usr/local/目录下面**

## 下载说明

> 安装脚本下载地址一：[GitHub](https://github.com/zer0131/centos_lnmp_setup/releases)

> 软件包下载地址：[百度云](http://pan.baidu.com/s/1o6uYAlc)
