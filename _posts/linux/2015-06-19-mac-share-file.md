---
layout: post
title: Mac VirtualBox 设置共享文件夹
category: linux
keywords: VirtualBox,MacOSX,linux,共享文件夹
description: Mac VirtualBox 设置共享文件夹
tags: MacOSX VirtualBox
---

* content
{:toc}

![VirtualBox](http://blog.zhangenrui.cn/vbox_logo.jpg)

## Mac安装VirtualBox

请到官网下载安装包：[下载](https://www.virtualbox.org/wiki/Downloads)

<!--more-->

根据使用手册安装即可。

在vbox中安装操作系统，这里就不作过多的介绍了。

## 设置共享文件夹

__1、配置虚拟机__

点击设置，如下图：
![vbox_1](http://blog.zhangenrui.cn/vbox_1.png)

建立共享文件夹，选择固定分配和自动挂在两个选项
![vbox_2](http://blog.zhangenrui.cn/vbox_2.png)

__2、进入虚拟操作系统设置挂载__

{% highlight sh %}
$ cd /mnt
$ mkdir -p mac_share
$ mount -t vboxsf share_file /mnt/mac_share
{% endhighlight %}

说明：share_file为本地建立的文件夹名称，mac_share为虚拟机文件夹名称

__3、设置开机自动挂载__

vim打开__/etc/rc.d/rc.local__

将命令`mount -t vboxsf share_file /mnt/mac_share`添加至末尾

最后重启一下虚拟机，这样你就可以在本机和虚拟机之间共享文件了。


