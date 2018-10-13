---
layout: post
title: Redis List类型
category: redis
keywords: redis,list,队列,链表
tags: redis list 队列 链表
---

* content
{:toc}

![redis-logo](http://blog.zhangenrui.cn/redis-logo.png)

## 类型介绍

list是一个链表结构，主要功能是push、pop、获取一个范围的所有值等等。操作中key理解为链表的名字。

<!--more-->

## 常用命令

### lpush

将所有指定的值插入到存于 key 的列表的头部

```sh
redis> lpush name "zer"
(integer) 1
redis> lpush name "ryan"
(integer) 2
```

**lpushx: 只有当key存在的时候才会从列表头部插入数据**

### rpush

向存于 key 的列表的尾部插入所有指定的值

```sh
redis> rpush name1 "zer"
(integer) 1
redis> rpush name1 "ryan"
(integer) 2
```

**rpushx: 只有当key存在的时候才会从列表尾部插入数据**

### lpop

移除并且返回 key 对应的 list 的第一个元素

```sh
redis> lpop name
"ryan"
```

### rpop

移除并返回存于 key 的 list 的最后一个元素

```sh
redis> lpop name1
"ryan"
```

### lrang

返回存储在 key 的列表里指定范围内的元素

```sh
redis> lrang name 0 0
"zer"
```

### llen

返回存储在 key 里的list的长度

```sh
redis> llen name
(integer) 1
```

## 类型应用

### 队列

## 基本数据结构