---
layout: post
title: OneFox系列之业务开发
category: onefox
keywords: OneFox,PHP,开发案例,框架
tags: OneFox PHP 案例 开发 框架
---

* content
{:toc}

![logo](http://7xj4mc.com1.z0.glb.clouddn.com/fox_logo.png)

## 控制器

### 控制器说明

OneFox控制器均放在应用目录下的**controller**目录；控制器的命名与类名相同、且首字母大写；自定义控制器请继承于框架核心控制器**onefox\Controller**。

控制器有两种模式，一种是模块模式，另一种无模块模式；在入口文件配置**MODULE_MODE**可开启或关闭模块模式，模块模式默认开启。

<!--more-->

开启模块模式意味着在**controller**目录下有模块目录，模块目录下才是控制器文件，如下:

{% highlight php %}
├─controller       controller目录
│  ├─index         模块目录
│  │  ├─Index.php  控制器
{% endhighlight %}

关闭模块模式，目录结构如下：

{% highlight php %}
├─controller       controller目录
│  ├─Index.php     控制器
{% endhighlight %}

控制器中的方法请以**Action**为后缀，如indexAction。

**特别说明：默认模块名index(如果开启模块模式)，默认控制器名Index，默认方法名index**

### 路由与参数

OneFox提供一个简单的路由，即根据URL中PATH信息定位控制器。

开启模块模式后，形如：http://www.xxx.com/mtest/ctest/atest的URL会路由到模块mtest下的ctest控制器中的atest方法。若关闭模块模式，那么去掉mtest后可以路由到ctest控制器中的atest方法。

URL中可添加PATH形式的参数，如：http://www.xxx.com/mtest/ctest/atest/ptest/test，URL中ptest为参数名、test为参数值。

**特别说明：如果控制器不想被路由到，使用抽象类即可**

## 模板

为了使用方便，OneFox没有使用模板语法，直接PHP内嵌HTML中，省去了学习模板语法的成本。

模板请放在应用目录下的**tpl**目录，且子目录结构应该与控制器的目录一一对应。举个栗子：开启模块模式的默认控制器对应的模板

{% highlight php %}
├─tpl              controller目录
│  ├─index         模块目录
│  │  ├─index.php  模板文件
{% endhighlight %}

从上面的目录结构可以看出，模板文件是以**php**为后缀的；与控制器文件名不同的是模板文件名需要小写。

引入其他模板

{% highlight php %}
<?php
$this->import('dir_name/tpl_name', array('param_key'=>'param_value'));
{% endhighlight %}

需要注意的是可以通过数组将本模板的参数传递给引入的模板。

## 模型

### 模型说明

模型在应用目录下**model**目录中，模型的命名根据您自己的喜好，我建议是后缀加上**Model**，如：TestModel.php；模型中的目录结构可以多级。

自定义模型请继承于**onefox\Model**，直接上代码说明

{% highlight php %}
<?php

namespace model;

use onefox\Model;

class TestModel extends Model {

    //数据库配置，这个一定要设置，对应的名称在database.php中
    protected $dbConfig = 'test';

    //对应的表名，这个可以不设置
    protected $table = 'test';

    public function insertTest() {
        //这个是访问数据库的方法，暂没有提供连贯操作
        return $this->db->query('select * from `posts`');
    }
}
{% endhighlight %}

### 数据库说明

数据库配置直接读取**config/database.php**，事先配置好即可。

DB类中常用方法：

{% highlight php %}
<?php

/**
 * 执行sql语句，如select, insert, update
 * 示例：
 * select: $db_obj->query('select * from `test` where `id`=:id', array('id'=>1))
 * delete: $db_obj->query('delete from `test` where `id`=:id', array('id'=>1))
 * insert: $db_obj->query('insert into `test`(name,age) values(:name,:age)', array('name'=>'ryan','age'=>20))
 * update: $db_obj->query('update `test` set name=:name where `id`=:id', array('name'=>'ryan', 'id'=>7))
 * @param string $query sql语句
 * @param array $params 参数
 * @param int $fetchmode 返回数据的模式，一般默认即可
 * @return 数据list
 */
public function query($query, $params = null, $fetchmode = \PDO::FETCH_ASSOC){//...}

/**
 * 返回一行
 * 示例: $db_obj->row('select * from `test` where `id`=:id', array('id'=>7))
 * @param string $query sql语句
 * @param array $params 参数
 * @param int $fetchmode 返回数据的模式，一般默认即可
 * @return
 */
public function row($query, $params = null, $fetchmode = \PDO::FETCH_ASSOC){//...}
{% endhighlight %}

**更多方法参见<a href="https://github.com/zer0131/OneFox/blob/master/onefox/DB.php" target="_blank" >onefox\DB</a>**

### Model中公共方法

* genInsertSql：生成插入语句
* genUpdateSql：生成更新语句
* insert：通用插入方法

## 类库

### 应用类库

应用类库在应用目录下的**lib**中，应用类库主要为本应用提供通用服务。

### 扩展类库

扩展类库在onefox同级目录的**class**目录下，这样设置方便其他应用调用。

还有一个需要注意的是，设置了扩展类库，在命名空间上是不需要命名根目录的，如：扩展类库下的Service类在Service目录下

{% highlight php %}
<?php
namespace Service;

class Service {
    //code...
}
{% endhighlight %}

从例子中可以看出，命名空间上是不需要加"class"目录的名称的。

**特别说明：使用应用类库或扩展类库，不建议将类直接写到类库的根目录下，最好有二级目录区分不同服务**

## 自定义函数库

自定义函数库在应用目录的**function**目录下，默认的文件名称为func.php。

可根据需要调整目录及文件名，分别就该FUNC_PATH、FUNC_NAME即可。

## CLI

在应用目录的**daemon**目录中，放相关命令行执行文件，一定注意的是，每个执行文件一定要引入**loader.php**
