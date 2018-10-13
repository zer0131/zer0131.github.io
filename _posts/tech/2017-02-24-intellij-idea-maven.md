---
layout: post
title: Intellij Idea创建maven慢解决方案
category: technology
tags: Intellij Maven
---

* content
{:toc}

使用Intellij Idea创建Maven webapp项目时非常缓慢，主要原因：

1. maven每次进行创建的时候回去网上下载artheType-catalog.xml
2. maven自带的仓库好像是国外的，访问起来比较慢

<!--more-->

## 第一步

将artheType-category.xml下载到本地，<a href="https://pan.baidu.com/s/1dF66J0d" target="_blank">点我下载</a>

在终端中使用如下命令

```
mv ~/Download/artheType-category.xml ~/.m2/repository/org/apache/maven/archetype/archetype-catalog/3.0.0/
```

如果没有arthetype文件夹使用下面命令

```
mv ~/Download/artheType-category.xml ~/.m2/
```

我这里安装的maven是3.3.9的版本，有需要的可以下载<a href="https://pan.baidu.com/s/1c2orULi" target="_blank">点我下载</a>

设置Maven Runner中的VM Options为**-DarchetypeCatalog=local**，如下图

![maven-00](http://blog.zhangenrui.cn/maven-00.jpg)

## 第二步

修改maven的下载源，这里我使用阿里云的下载源

在我的maven安装目录conf目录中，设置settings.xml，在<mirrors>标签中添加如下代码

```
<mirror>    
    <id>nexus-aliyun</id>    
    <mirrorOf>central</mirrorOf>      
    <name>Nexus aliyun</name>    
    <url>http://maven.aliyun.com/nexus/content/groups/public</url>  
</mirror> 
```

最后在创建maven arthetype webapp时，创建速度会是飞一般的感觉！！！

