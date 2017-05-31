---
layout: post
title: OneFox系列之配置
category: onefox
keywords: OneFox,PHP,配置,框架
tags: OneFox PHP 配置 框架
---

* content
{:toc}

![logo](http://7xj4mc.com1.z0.glb.clouddn.com/fox_logo.png)

## 入口文件

应用的入口文件是**app/public/index.php**，可在文件中设置常量配置。

## 配置常量

<!--more-->

{% highlight php %}
ONEFOX_VERSION  版本号
REQUEST_ID  请求唯一标识，常用于调试
IS_CLI  是否是cli模式
APP_PATH  应用路径，需在index.php中设置
ONEFOX_PATH  框架路径，需在index.php中设置
DS  目录分隔符，可设置，默认"/"
VENDOR_PATH  composer类库，可设置，默认在vendor目录下
MODULE_MODE  是否开启模块模式，可设置，默认开启
DEBUG  是否开启调试模式，可设置，默认关闭
LOG_PATH  日志记录目录，可设置，默认在APP_PATH下的log目录
CONF_PATH  配置文件目录，可设置，默认在APP_PATH下的config目录
TPL_PATH  模板存放目录，可设置，默认在APP_PATH下的tpl目录
LIB_PATH 扩展类库目录，可设置，默认在APP_PATH下的lib目录
FUNC_PATH 自定义函数库目录，可设置，默认在APP_PATH下的function目录
FUNC_NAME 自定义函数库文件名，可设置，默认为func.php
DEFAULT_MODULE  默认执行模块，可设置，默认index(MODULE_MODE开启有效)
DEFAULT_CONTROLLER  默认执行控制器，可设置，默认Index
DEFAULT_ACTION  默认执行方法，可设置，默认index
DEFAULT_TIMEZONE 默认时区，可设置，默认Asia/Shanghai
XSS_MODE  是否开启XSS过滤，可设置，默认开启
ADDSLASHES_MODE  是够开启addslashes，可设置，默认关闭
MAGIC_QUOTES_GPC  php5.4.0+版本关闭
{% endhighlight %}

## 配置文件通用写法

{% highlight php  %}
<?php
$common = array(
    'your_key' => 'your_value',
);

$online = array();

$dev = array();

return DEBUG ? array_merge($common, $dev) : array_merge($common, $online);
?>
{% endhighlight  %}

## 应用配置

应用默认的配置文件为CONF_PATH/config.php，可将应用中的配置写在该文件中，获取方式如下

{% highlight php  %}
<?php

$configVal = Config::get('config.your_key');

?>
{% endhighlight  %}

## 数据库配置

数据库配置文件为database.php(**文件名不可更改**)，你可以为应用配置多数据库，不同数据库用不同的键名表示，默认键名为default

{% highlight php  %}
<?php
$online = array(
    'default' => array(
        'host' => '127.0.0.1',
        'user' => 'root',
        'port' => 3306,
        'password' => '',
        'dbname' => 'test'
    )
);
?>
{% endhighlight %}

## 缓存配置

缓存配置文件为cache.php(**文件名不可更改**)，四种缓存方式：file,memcache,memcached,redis

{% highlight php  %}
<?php
$common = array(
    'type' => 'redis', //file, memcache, memcached, redis四种缓存方式
    'file' => array(
        'path' => APP_PATH . DS . 'cache',
        'expire' => 0,
        'prefix' => 'onefox_'
    ),
    'memcache' => array(
        'expire' => 0,
        'prefix' => 'onefox_',
        'servers' => array(
            array(
                'host' => '127.0.0.1',
                'port' => 11211,
                'persistent' => false,
                'weight' => 10
            ),
        )
    ),
    'redis' => array(
        'expire' => 0,
        'prefix' => 'onefox_',
        'server' => array(
            'host' => '127.0.0.1',
            'port' => 6379
        )
    )
);
?>
{% endhighlight %}

## 日志配置

日志配置文件为log.php(**文件名不可更改**)

{% highlight php %}
<?php
$common = array(
    'default' => array(
        'ext' => 'log',//日志文件扩展名
        'date_format' => 'Y-m-d H:i:s',//日期格式
        'filename' => '',//日志文件名
        'log_path' => '',//日志存放目录
        'prefix' => '',//日志文件名前缀
        'log_level' => 'info',//日志输出级别
    )
);
?>
{% endhighlight %}

## session设置

session配置文件session.php(**文件名不可更改**)

{% highlight php %}
<?php
$common = array(
    'auto_start' => true,//自动开启session, 默认关闭
    'save_path' => APP_PATH . DS . 'session',//session存储路径
    'name' => 'PHPSESSID',//session名称
    'save_handler' => 'files',//session存储方式
    'serialize_handler' => 'php',//PHP标准序列化
    'gc_maxlifetime' => 1440,//过期时间\
    'gc_probability' => 1,
    'gc_divisor' => 100,//建议设置1000-5000, 概率=session.gc_probability/session.gc_divisor（1/1000）, 页面访问越频繁概率越小
    'cookie_lifetime' => 0,//cookie存活时间（0为直至浏览器重启，单位秒）
    'cookie_path' => '/',//cookie的有效路径
    'cookie_httponly' => "",//httponly标记增加到cookie上(脚本语言无法抓取)
    'cookie_domain' => '',//cookie的有效域名
    'use_trans_sid' => 0,//trans_sid支持(默认0)
    'use_cookies' => 1,//使用cookies在客户端保存会话
    'use_only_cookies' => 1,//去保护URL中传送session id的用户
    'cache_limiter' => 'nocache',//HTTP缓冲类型(nocache,private,pblic)
    'cache_expire' => 180,//文档过期时间(分钟)
    'bug_compat_42' => 1,//全局初始化session变量
    'bug_compat_warn' => 1,
    'referer_check' => '',//防止带有ID的外部URL
    'hash_function' => 0,//hash方法{0:md5(128 bits),1:SHA-1(160 bits)}
    'hash_bits_per_character' => 4,//当转换二进制hash数据奥可读形式是，每个字符保留位数
);
?>
{% endhighlight %}

## 配置读取

使用框架提供的**Config类**读取相关配置，下面是代码示例

{% highlight php  %}
<?php

$config = onefox\Config::get('filename.config_key');

{% endhighlight  %}

**一定主要使用"."将配置文件名和配置键名分开**
