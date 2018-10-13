---
layout: post
title: RUIBlog开发手记之RBAC
category: php
keywords: RUIBlog,PHP
tags: RUIBlog PHP ThinkPHP RBAC
---

* content
{:toc}

## RBAC数据表

众所周知，权限是离不开数据表的支持。使用THinkPHP中的RBAC我们需要使用以下几个表：

iqishe_access：权限表

iqishe_role：角色表

iqishe_node：节点表

iqishe_role_user：角色用户关系表

<!--more-->

关于表的详细结构我已经在《RUIBlog开发手记之需求中说过》，也在word中做了记录，可以点击下面查看：

[http://pan.baidu.com/share/link?shareid=2388552896&uk=1846304022](http://pan.baidu.com/share/link?shareid=2388552896&uk=1846304022)

我们看到在所需的表中没有看到关于用户表的任何信息，为什么呢？原因其实很简单，用户表是需要我们自己定义的，然后卸载配置中的，关于配置，我们后面说，还是先说收上面的表都是干啥使的。

**iqishe_access**：其实就是存储节点和角色的关系的，将节点和角色对应起来存在此表，那么角色对相应的节点才有访问权限。否则你就无权访问。

**iqishe_role**：存储你在系统中所定义的角色，值得注意的是，角色下可以定义子角色，在RUIBlog中我暂时没有使用子类角色的功能，所以暂不作介绍。

**iqishe_node**：存储节点，节点分为三个级别，1级标识应用，2级标识模块，3级标识操作，注意，如果你要对某个操作进行授权，那么你首先要对其所属的模块和应用授权。

**iqishe_role_user**：将用户的id和角色的id关联起来存入此表，这样，该用户就属于某个角色了。角色和用户本就是多对多的关系，只不过在RUIBlog系统中我使用的是一对多的关系，即一个用户只能属于一个角色，一个角色可以指派给多个用户。

以上就是我个人对这几个表的理解，希望对大家有用处吧！

## RBAC配置

使用之前，我们还需要在config.php中添加如下配置：

'USER_AUTH_ON' => true------开启权限

'USER_AUTH_TYPE' => 2------默认认证类型 1 登录认证 2 实时认证(当你修改了角色权限，实时生效，无需重新登录)

'USER_AUTH_KEY' => 'authId'------用户认证SESSION标记(这里可以改成你自己的标记)

'ADMIN_AUTH_KEY' => 'administrator'------管理员认证SESSION标记(这里可以改成你自己的标记)

'USER_AUTH_MODEL' => 'Users'------默认验证数据表模型(修改成你自己的用户表名称即可，这就是我上面提到过的如何将自己的用户表集成到RBAC中)

'AUTH_PWD_ENCODER' => 'md5'------用户认证密码加密方式

'USER_AUTH_GATEWAY' => '/Public/login'------默认认证网关

'NOT_AUTH_MODULE' => 'Public'------默认无需认证模块

'REQUIRE_AUTH_MODULE' => ''------默认需要认证模块，不填留空即可

'NOT_AUTH_ACTION' => ''------默认无需认证操作，不填留空即可

'REQUIRE_AUTH_ACTION' => ''------默认需要认证操作，不填留空即可

'GUEST_AUTH_ON' => false------是否开启游客授权访问

'GUEST_AUTH_ID' => 0------游客的用户ID

'RBAC_ROLE_TABLE' => 'iqishe_role'------配置角色表，前缀依个人不同可更改

'RBAC_USER_TABLE'=>'iqishe_role_user'------配置角色用户关系表，前缀依个人不同可更改

'RBAC_ACCESS_TABLE'=>'iqishe_access'------配置权限表，前缀依个人不同可更改

'RBAC_NODE_TABLE'=>'iqishe_node'------配置节点表，前缀依个人不同可更改

## RBAC使用

### 1、新增角色

![ruiblog_rbac_1](http://blog.zhangenrui.cn/ruiblog_rbac_1.jpg)

![ruiblog_rbac_2](http://blog.zhangenrui.cn/ruiblog_rbac_2.jpg)

### 2、角色授权

![ruiblog_rbac_3](http://blog.zhangenrui.cn/ruiblog_rbac_3.jpg)

![ruiblog_rbac_4](http://blog.zhangenrui.cn/ruiblog_rbac_4.jpg)

![ruiblog_rbac_5](http://blog.zhangenrui.cn/ruiblog_rbac_5.jpg)

![ruiblog_rbac_6](http://blog.zhangenrui.cn/ruiblog_rbac_6.jpg)

### 3、新增用户

![ruiblog_rbac_7](http://blog.zhangenrui.cn/ruiblog_rbac_7.jpg)

这就是我在开发RUIBlog时使用RBAC的一些见解和操作方法，THinkPHP官方网站有很多关于RBAC的视频教程，大家可以将RUIBlog作为一个案例结合教程来看，我想更能提高自己吧！！！