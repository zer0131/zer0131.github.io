---
layout: post
title: Redis String类型
category: redis
keywords: redis,string,key-value
tags: redis string key-value
---

* content
{:toc}

![redis-logo](http://7xj4mc.com1.z0.glb.clouddn.com/redis-logo.png)

## 类型介绍

String是redis最基本也是最简单的数据类型，典型的key-value结构，value可以是字符串、数值、浮点数。例如：key为name，值为ryan，见下图

<!--more-->

![string-1](http://7xj4mc.com1.z0.glb.clouddn.com/string-img-1.png)

## 常用命令

### set

设置一个key的value

```sh
redis> set name "ryan"
OK
```

### get

获取一个key的value

```sh
redis> get name
"ryan"
```

### setex

设置key对应字符串value，并且设置key在给定的seconds时间之后超时过期

{% highlight sh %}
redis> setex name 10 "ryan"
OK
{% endhighlight %}

### setnx

将key设置值为value，如果key不存在

```sh
redis> setnx name "ryan"
(integer) 1
redis> setnx name "ryan"
(integer) 0
```

### append

如果key已经存在，并且值为字符串，那么这个命令会把value追加到原来值（value）的结尾。 如果key不存在，那么它将首先创建一个空字符串的key，再执行追加操作

```sh
redis> append name "ryan"
(integer) 4
redis> append name "zhang"
(integer) 9
```

### incr

对存储在指定key的数值执行原子的加1操作，如果指定的key不存在，那么在执行incr操作之前，会先将它的值设定为0

```sh
redis> incr age
(integer) 1
```

### decr

对key对应的数字做减1操作。如果key不存在，那么在操作之前，这个key对应的值会被置为0

```sh
redis> set age 25
OK
redis> decr age
(integer) 24
```

<a href="https://redis.io/commands#string" target="_blank">更多命令</a>

## 类型应用

### 数据行缓存

将从db中取出的数据缓存，以php为例，将从db中取出的关联数组转化为json串存入redis(php扩展基于phpredis)

```php
<?php

/**
 * 缓存数据行示例
 * @param array $data db取出的关联数组
 * @return bool
 */
function cacheData($data) {
    $redis = new Redis();
    $redis->connect('127.0.0.1', 6379);
    $key = 'data:'.$data['id'];//id为数据行的主键
    $value = json_encode($data);
    return $redis->set($key, $value);
}

```

### 锁

在多任务环境下，多个任务对同一个资源进行操作时会有操作错乱的问题，因此当某一个任务拿到资源的操作权，我们对其进行加锁，对资源操作完后再释放锁，这样在加锁期间其他的任务等待锁释放后再操作资源。

```php
<?php

class Lock {

    private $_redis;
    
    public function __construct() {
        $this->_redis = new Redis();
        $this->redis->connect('127.0.0.1', 6379);
    }

    public function acquire($lockName, $acquireTime = 10, $lockTimeout = 10) {
        $value = uniqid();
        $key = 'lock:'.$lockName;
        $lockTimeout = intval($lockTimeout);
        $end = time() + $acquireTime;
        while (time() < $end) {
            if ($this->_redis->setnx($key, $value)) {
                $this->_redis->expire($key, $lockTimeout);
                return $value;
            } elseif (!$this->_redis->ttl($key)) {
                $this->_redis->expire($key, $lockTimeout);
            }
            usleep(1000);
        }
        return false;
    }

    public function release($lockName, $value) {
        $key = 'lock:'.$lockName;
        if ($this->_redis->get($key) == $value) {
            $this->_redis->del($key);
            return true;
        }
        return false;
    }
}

$lockObj = new Lock();
$lock = $lockObj->acquire('test');
if ($lock) {
    //do something...
    $lockObj->release('test', $lock);
}

```

## 基本数据结构

### sds结构

sds是redis底层的数据结构，也是redis最核心、最基本的数据结构；redis存储字符串类型的数据都会使用该数据结构。

在redis源码中<a href="https://github.com/zer0131/zer0131.github.io/blob/master/code/redis/sds.h" target="_blank">sds.h</a>和<a href="https://github.com/zer0131/zer0131.github.io/blob/master/code/redis/sds.c" target="_blank">sds.c</a>两个文件为sds的实现。

redis4.0中的sds结构会根据存入数据的大小选择，所以定义了存储不同长度的结构体

```c
struct __attribute__ ((__packed__)) sdshdr5 {
    unsigned char flags; /* 3 lsb of type, and 5 msb of string length */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr8 {
    uint8_t len; /* 字符串使用长度 */
    uint8_t alloc; /* 除头和空终止符的长度 */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];/* 存放字符的数组 */
};
struct __attribute__ ((__packed__)) sdshdr16 {
    uint16_t len; /* used */
    uint16_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr32 {
    uint32_t len; /* used */
    uint32_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr64 {
    uint64_t len; /* used */
    uint64_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
```

根据传入数据的大小确定选用的结构体类型

```c
static inline char sdsReqType(size_t string_size) {
    if (string_size < 1<<5)
        return SDS_TYPE_5;//使用sdshdr5
    if (string_size < 1<<8)
        return SDS_TYPE_8;//使用sdshdr8
    if (string_size < 1<<16)
        return SDS_TYPE_16;//使用sdshdr16
#if (LONG_MAX == LLONG_MAX)
    if (string_size < 1ll<<32)
        return SDS_TYPE_32;//使用sdshdr32
#endif
    return SDS_TYPE_64;//使用sdshdr64
}
```

### t_string文件

<a href="https://github.com/zer0131/zer0131.github.io/blob/master/code/redis/t_string.c" target="_blank">t_string.c</a>封装了关于字符串的上层操作，如set操作会调用**setCommand**方法

```c
void setCommand(client *c) {
    int j;
    robj *expire = NULL;
    int unit = UNIT_SECONDS;
    int flags = OBJ_SET_NO_FLAGS;

    //解析命令参数
    for (j = 3; j < c->argc; j++) {
        char *a = c->argv[j]->ptr;
        robj *next = (j == c->argc-1) ? NULL : c->argv[j+1];

        if ((a[0] == 'n' || a[0] == 'N') &&
            (a[1] == 'x' || a[1] == 'X') && a[2] == '\0' &&
            !(flags & OBJ_SET_XX))
        {
            flags |= OBJ_SET_NX;
        } else if ((a[0] == 'x' || a[0] == 'X') &&
                   (a[1] == 'x' || a[1] == 'X') && a[2] == '\0' &&
                   !(flags & OBJ_SET_NX))
        {
            flags |= OBJ_SET_XX;
        } else if ((a[0] == 'e' || a[0] == 'E') &&
                   (a[1] == 'x' || a[1] == 'X') && a[2] == '\0' &&
                   !(flags & OBJ_SET_PX) && next)
        {
            flags |= OBJ_SET_EX;
            unit = UNIT_SECONDS;
            expire = next;
            j++;
        } else if ((a[0] == 'p' || a[0] == 'P') &&
                   (a[1] == 'x' || a[1] == 'X') && a[2] == '\0' &&
                   !(flags & OBJ_SET_EX) && next)
        {
            flags |= OBJ_SET_PX;
            unit = UNIT_MILLISECONDS;
            expire = next;
            j++;
        } else {
            addReply(c,shared.syntaxerr);
            return;
        }
    }

    c->argv[2] = tryObjectEncoding(c->argv[2]);//对设置值得转换

    //调用公用创建方法
    setGenericCommand(c,flags,c->argv[1],c->argv[2],expire,unit,NULL,NULL);
}
```

### 编码方式

* OBJ_ENCODING_RAW
* OBJ_ENCODING_INT
* OBJ_ENCODING_EMBSTR

三种编码方式，根据存入的数据是否是整数、大小来决定使用哪种