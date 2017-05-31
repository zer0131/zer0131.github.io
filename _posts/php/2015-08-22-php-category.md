---
layout: post
title: PHP实现无限分类
category: php
keywords: 无限分类，PHP
description: PHP实现无限分类的几种方式
tags: PHP 无限分类
---

* content
{:toc}

在很多地方我们都需要用到无限分类，今天给大家简单的介绍一下关于PHP实现无限分类的几种方式。

## 递归方式

这种方式也是大家比较常用的一种方式，先来看一下主要的表结构。

> **id**: 自增id
> **name**: 分类名称
> **parent_id**: 父类id，顶级类值为0
> **sort**: 排序值

<!--more-->

代码部分：

{% highlight php %}
<?php

	/**
     * @param list array 二维数组
     * @param pid int 父级编号
     * @parma level int 层级
     * @param html string html输出前缀
     */

	function tree($list, $pid = 0, $level = 1, $html = ' -- '){
		$tree = array();
        foreach ($list as $v) {
            if ($v['parent_id'] == $pid) {
                $v['sort'] = $level;
                $v['html'] = '|' . str_repeat($html, $level);
                $tree[] = $v;
                $tree = array_merge($tree, tree($list, $v['id'], $level + 1, $html));
            }
        }
        return $tree;
	}
?>
{% endhighlight %}

## 引用方式

其实PHP引用是一个不错的东东，可以使用引用来组织数据。

{% highlight php %}
<?php
	/**
     * @param $data 需要处理的数组
     */
    function tree($data){
        $items = array();
        foreach ($data as $val) {
            $items[$val['id']] = $val;
        }
        unset($data);
        $tree = array();
        foreach ($items as $item) {
            if (isset($items[$item['parent_id']])) {
                $items[$item['parent_id']]['son'][] = &$items[$item['id']];
            } else {
                $tree[] = &$items[$item['id']];
            }
        }
        return $tree;
    }
?>
{% endhighlight %}

## 基于左右编码值

这种方式的关键点在于数据库的设计，主要表结构如下：

> **id**: 自增id
> **name**: 分类名称
> **lft**: 左值
> **rgt**: 右值
> **level**: 层级数

具体看一个比较形象的例子，如水果的分类

Food : 食物 
Fruit : 水果 
Red : 红色 
Cherry: 樱桃 
Yellow: 黄色 
Banana: 香蕉 
Meat : 肉类 
Beef : 牛肉 
Pork : 猪肉

把上面的分类，组织成一个二叉树的结构，并在每个分类的左右标值，如下图：

![分类图](http://7xj4mc.com1.z0.glb.clouddn.com/php_category_1.png)

从图中来看，我们要得到水果下面的所有分类，那么只需要用到下面的SQL语句：

{% highlight sql %}
SELECT * FROM tree WHERE lft BETWEEN 2 AND 11;
{% endhighlight %}

通过下面代码便可很快的输出树形结构

{% highlight php %}
<?php
	function tree($root){
		// 得到根节点的左右值
	    $result = mysql_query("
	        SELECT lft, rgt
	        FROM tree
	        WHERE name = '" . $root . "'
	        ;"
	    );
	    $row = mysql_fetch_array($result);
	    // 准备一个空的右值堆栈
	    $right = array();
	    // 获得根基点的所有子孙节点
	    $result = mysql_query("
	        SELECT name, lft, rgt
	        FROM tree
	        WHERE lft BETWEEN '" . $row['lft'] . "' AND '" . $row['rgt'] ."'
	        ORDER BY lft ASC
	        ;"
	    );
	    // 显示每一行
	    while ($row = mysql_fetch_array($result)) {
	        // only check stack if there is one
	        if (count($right) > 0) {
	            // 检查我们是否应该将节点移出堆栈
	            while ($right[count($right) - 1] < $row['rgt']) {
	                array_pop($right);
	            }
	        }
	        // 缩进显示节点的名称
	        echo str_repeat('  ',count($right)) . $row['name'] . "\n";
	        // 将这个节点加入到堆栈中
	        $right[] = $row['rgt'];
	    }
	}
?>
{% endhighlight %}
