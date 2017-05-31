---
layout: post
title: python监测linux进程
category: technology
keywords: python,linux,进程
description: 使用python轻松编写监测linux进程是否正常运行的脚本。
tags: Python linux 进程 监控
---

* content
{:toc}

![python logo](http://7xj4mc.com1.z0.glb.clouddn.com/python_logo.png)

## 概述
很多时候我们都需要监测linux服务器中的一个或多个进程是否正常运行，并能通过邮件的方式通知系统管理员。使用python编写一个监测进程是否运行正常的脚本是很方面和高效的，那么我们就使用python中的subprocess模块并结合linux命令来简单实现一个监测脚本。

<!--more-->

## subprocess模块简介
subprocess的主要作用就是启动一个进程并与之通信。
这里我们主要用subprocess中的Popen类。

__subprocess.Popen(args, bufsize=0, executable=None, stdin=None, stdout=None, stderr=None, preexec_fn=None, close_fds=False, shell=False, cwd=None, env=None, universal_newlines=False, startupinfo=None, creationflags=0)__

参数args可以是字符串或者序列类型（如：list，元组），用于指定进程的可执行文件及其参数。如果是序列类型，第一个元素通常是可执行文件的路径。我们也可以显式的使用executeable参数来指定可执行文件的路径。

参数stdin, stdout, stderr分别表示程序的标准输入、输出、错误句柄。他们可以是PIPE，文件描述符或文件对象，也可以设置为None，表示从父进程继承。

如果参数shell设为true，程序将通过shell来执行。

参数env是字典类型，用于指定子进程的环境变量。如果env = None，子进程的环境变量将从父进程中继承。

subprocess.PIPE，在创建Popen对象时，subprocess.PIPE可以初始化stdin, stdout或stderr参数。表示与子进程通信的标准流。

subprocess.STDOUT，创建Popen对象时，用于初始化stderr参数，表示将错误通过标准输出流输出。

## 代码实现
创建文件check.py

{% highlight python %}
#!/usr/bin/env python
# -*- coding: utf-8 -*-

import subprocess,os,time

#检测进程
def check(findKey):
    p1 = subprocess.Popen(['ps','-ef'],stdout=subprocess.PIPE)
    p2 = subprocess.Popen(['grep',findKey],stdin=p1.stdout,stdout=subprocess.PIPE)
    r = p2.stdout.readlines()
    flag = False
    #print r
    # print len(r)
    if len(r) > 0:
        for v in r:
            restr = "python /path/to/"+findKey
            if v.find(restr) != -1:
                flag = True
                break
    else:
        flag = False

    return flag

#运行进程
def run(cmd, out):
    realCmd = 'nohup python /path/to/' + cmd + ' > ' + out + ' &'
    os.system(realCmd)

if __name__ == '__main__':
	while True:
		if check('test.py') is False:
            run('test.py', '/tmp/test_log.txt')
            #do something
            time.sleep(3)
		time.sleep(3600) #间隔1小时检测一次
{% endhighlight %}

## 配置计划任务
linux中的计划任务是使用crontab来配置的，在linux命令行下，键入以下命令：
{% highlight sh %}
crontab -e
{% endhighlight %}

至此，linux会呈现一个vim编辑界面，ps：vim相关知识请 [百度](http://www.baidu.com) ，这里我为大家提供一些简单的入门命令 [猛戳一下](http://appryan.com/2015/05/16/vim-basic-cmd/)。关于crontab的配置格式请参考下图：

![crontab](http://7xj4mc.com1.z0.glb.clouddn.com/crontab.png)

{% highlight vim %}
0 8 * * * /usr/local/python /path/to/check.py
{% endhighlight %}

上面代码说明每天的8点开始运行监测脚本。

（完）

