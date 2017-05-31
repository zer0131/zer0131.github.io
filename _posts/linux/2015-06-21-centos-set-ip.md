---
layout: post
title: Centos设置固定ip
category: linux
keywords: Centos,静态ip
description: Centos下设置静态ip，方便以后查询设置。
tags: linux centos ip
---

* content
{:toc}

![centos logo](http://7xj4mc.com1.z0.glb.clouddn.com/centos_logo.png)

## 网卡配置

**ifconfig**查看网络设置
<!--more-->
![centos_ip_1](http://7xj4mc.com1.z0.glb.clouddn.com/centos_ip_1.png)

vim打开**/etc/sysconfig/network-scripts/ifcfg-eth0**
![centos_ip_2](http://7xj4mc.com1.z0.glb.clouddn.com/centos_ip_2.png)

**DEVICE=eth0**描述网卡对应的设备别名，例如ifcfg-eth0的文件中它为eth0

**BOOTPROTO=static**设置网卡获得ip地址的方式，可能的选项为static，dhcp或bootp，分别对应静态指定的 ip地址，通过dhcp协议获得的ip地址，通过bootp协议获得的ip地址

**BROADCAST=192.168.1.255**对应的子网广播地址

**HWADDR=08:00:27:26:F1:83**对应的网卡物理地址

**IPADDR=192.168.1.131**如果设置网卡获得 ip地址的方式为静态指定，此字段就指定了网卡对应的ip地址

**NETMASK=255.255.255.0**网卡对应的网络掩码

**NETWORK=192.168.1.1**网卡对应的网络地址

## 网关配置
vim打开**/etc/sysconfig/network**
![centos_ip_3](http://7xj4mc.com1.z0.glb.clouddn.com/centos_ip_3.png)

**NETWORKING=yes**表示系统是否使用网络，一般设置为yes。如果设为no，则不能使用网络，而且很多系统服务程序将无法启动

**HOSTNAME=centos**设置本机的主机名，这里设置的主机名要和/etc/hosts中设置的主机名对应

**GATEWAY=192.168.1.1**设置本机连接的网关的IP地址

## DNS配置
vim打开**/etc/resolv.conf**
![centos_ip_4](http://7xj4mc.com1.z0.glb.clouddn.com/centos_ip_4.png)

**nameserver**即是DNS服务器IP地址

## 重启网络服务

{% highlight sh %}
$ service network restart
{% endhighlight %}

