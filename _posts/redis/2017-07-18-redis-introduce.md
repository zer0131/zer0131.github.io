---
layout: post
title: Redis入门
category: redis
keywords: redis,介绍,简介,入门
tags: redis 简介 入门
---

* content
{:toc}

![redis-logo](http://7xj4mc.com1.z0.glb.clouddn.com/redis-logo.png)

## 简介

redis是一个key-value存储系统。和Memcached类似，它支持存储的value类型相对更多，包括string(字符串)、list(链表)、set(集合)、zset(sorted set --有序集合)和hash（哈希类型）。这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作，而且这些操作都是原子性的。在此基础上，redis支持各种不同方式的排序。与memcached一样，为了保证效率，数据都是缓存在内存中。区别的是redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。

<!--more-->

Redis 是一个高性能的key-value数据库。 redis的出现，很大程度补偿了memcached这类key/value存储的不足，在部 分场合可以对关系数据库起到很好的补充作用。它提供了Java，C/C++，C#，PHP，JavaScript，Perl，Object-C，Python，Ruby，Erlang等客户端，使用很方便。

Redis支持主从同步。数据可以从主服务器向任意数量的从服务器上同步，从服务器可以是关联其他从服务器的主服务器。这使得Redis可执行单层树复制。存盘可以有意无意的对数据进行写操作。由于完全实现了发布/订阅机制，使得从数据库在任何地方同步树时，可订阅一个频道并接收主服务器完整的消息发布记录。同步对读取操作的可扩展性和数据冗余很有帮助。

redis的官网地址，非常好记，是<a href="https://redis.io/" target="_blank">redis.io</a>

## 数据类型

### String(字符串)

字符串是Redis中最基本的数据类型，它能够存储任何类型的字符串，包含二进制数据。可以用于存储邮箱，JSON化的对象，甚至是一张图片，一个字符串允许存储的最大容量为512MB。字符串是其他四种类型的基础，与其他几种类型的区别从本质上来说只是组织字符串的方式不同而已。

### List(列表)

列表类型(list)用于存储一个有序的字符串列表，常用的操作是向队列两端添加元素或者获得列表的某一片段。

### Set(集合)

集合在概念在高中课本就学过，集合中每个元素都是不同的，集合中的元素个数最多为2的32次方-1个，集合中的元素师没有顺序的。

### Zset(有序集合)

有序集合类型与集合类型的区别就是他是有序的。有序集合是在集合的基础上为每一个元素关联一个分数，这就让有序集合不仅支持插入，删除，判断元素是否存在等操作外，还支持获取分数最高/最低的前N个元素。有序集合中的每个元素是不同的，但是分数却可以相同。

### Hash(散列表)

散列类型相当于Java中的HashMap，他的值是一个字典，保存很多key，value对，每对key，value的值个键都是字符串类型，换句话说，散列类型不能嵌套其他数据类型。一个散列类型键最多可以包含2的32次方-1个字段

## 安装

以安装4.0为例

```
wget http://7xkyq4.com1.z0.glb.clouddn.com/redis/redis-4.0.0.tar.gz
tar -xf redis-4.0.0.tar.gz
cd redis-4.0.0
make && make test
make PREFIX=/your/path install
cd ..
```
完整安装脚本：<a href="https://github.com/zer0131/centos_lnmp_setup/blob/master/install_redis.sh" target="_blank">install_redis.sh</a>

## 运行

```
cd /your/path/redis-4.0.0/bin
./redis-server
```

进入命令行

```
./redis-cli
```