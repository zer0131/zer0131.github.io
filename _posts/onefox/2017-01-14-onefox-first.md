---
layout: post
title: OneFox系列之入门
category: onefox
keywords: OneFox,PHP,入门,框架
tags: OneFox PHP 入门 框架
---

* content
{:toc}

## 简介

![logo](http://7xj4mc.com1.z0.glb.clouddn.com/fox_logo.png)

OneFox是一个快速、简单的基于MVC和面向对象的轻量级PHP开发框架，遵循Apache2开源协议发布，简单阅读使用手册即可快速开发自己的Web应用。OneFox主要有以下优点：

* 框架核心不臃肿，加载速度快
* 更适合api之类的接口业务
* 模板不依赖模板引擎，减少学习模板语言的成本
* 支持composer安装类库

<!--more-->

## 环境要求

* PHP5.3+
* 支持Windows/Unix服务器环境
* 可运行于包括Apache、IIS和nginx在内的多种WEB服务器和模式
* 缓存支持文件、redis、memcache等多种方式
* 目前仅支持连接MySQL数据库

## 安装

```
git clone https://github.com/zer0131/OneFox.git /home/project
```

## 目录结构

### 部署目录结构

```
project           WEB部署目录(或者子目录)
├─LICENSE         LICENSE文件
├─README.md       README文件
├─composer.json   本项目composer说明文件
├─app             应用目录
├─class           公共类文件
├─vendor          通过composer安装的类库目录(如果需要)
└─onefox          框架目录
```

### 应用目录结构

```
├─app
│  ├─cache            文件缓存存放目录
│  ├─config           配置目录
│  ├─controller       controller目录
│  │  ├─index         模块目录(如果开启)
│  ├─lib              应用类库目录
│  ├─model            model目录
│  ├─tpl              模板目录
│  │  ├─comm          公共模板
│  │  ├─index         模块目录(如果开启)
│  │  │  ├─index      控制器目录
│  ├─logs             日志文件存放目录
│  ├─daemon           cli脚本目录
│  ├─session          session文件存放目录
│  └─public           入口目录，可存放资源文件等
```

### 框架目录结构

```
├─onefox
│  ├─caches             缓存类目录
│  ├─tpl                系统模板目录
│  ├─C.php              公共函数文件
│  ├─Cache.php          缓存抽象文件
│  ├─Config.php         配置类文件
│  ├─Controller.php     抽象控制器文件
│  ├─Crypt.php          加解密类文件
│  ├─Curl.php           Curl类文件
│  ├─DB.php             数据库范文类文件
│  ├─Dispatcher.php     路由解析类文件
│  ├─Log.php            日志类文件
│  ├─Model.php          基础Model类文件
│  ├─Request.php        请求类文件
│  ├─Response.php       响应类文件
│  ├─View.php           视图解析类文件
│  ├─functions.php      常用函数文件
│  ├─Base.php           框架基础抽象类文件
│  └─OneFox.php         框架入口文件
```

## 开发规范

* 使用命名空间，并且前缀应与对应的目录名称相同，如：命名空间为controller\api，则对应的文件目录controller/api

* controller和model中的类文件名请以大写字母开头且后面全部小写，如：controller中的Index控制器

* 如果开启模块功能，那么controller和model中的模块目录名请使用小写，如：controller下的api模块，目录名为api

* 模板文件中模块目录名与controller中的模块目录名一致，控制器目录名请用小写字母

* 应用类库lib和公共类库class中的类请遵照psr4规范(类文件名建议使用驼峰命名法命名)

* 函数的命名使用小写字母和下划线的方式，如：find_path

* 方法的命名使用驼峰法，并且首字母小写或者使用下划线，私有方法请使用下划线开头

* 常量以大写字母和下划线命名，如：HAS_ONE和MANY_TO_MANY

## composer概述

框架自动识别composer中的vendor目录, 请在composer.json中引入你要使用的类库

安装请使用下面命令

```
curl -sS https://getcomposer.org/installer | php
```
或
```
php -r "readfile('https://getcomposer.org/installer');" | php
```
或
```
wget http://7xkyq4.com1.z0.glb.clouddn.com/php/composer.phar
```

<a href="http://docs.phpcomposer.com/" target="_blank">我想进一步学习composer</a>
