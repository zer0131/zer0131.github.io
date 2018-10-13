---
layout: post
title: RUIBlog开发手记之无限分类
category: php
keywords: 无限分类,RUIBlog
tags: 无限分类 RUIBlog
---

* content
{:toc}

又是有段时间没有写东西了，还总有些不习惯。最近半个月一直在研究支付宝API，不过大家可不要误解，这片文章并不是写关于支付宝API的东东(支付宝API如何集成，往后我会总结出来供大家参考的)。这篇文章，我还是说说我自己做的这个博客吧，思来想去，还是说个老掉牙的话题—无限分类。

关于“无限分类”实现的方法其实蛮多的，我的理解无非是三种：**递归**，**AJAX**，**亲缘关系**。从效率上讲，后两种交第一种好些；从实现难度上讲，最后一种交优。

<!--more-->

RUIBlog的分类使用的就是递归的思想来实现的。这个分类实际上就是对网站栏目的一个管理，只不过你可以在栏目下面无限添加子栏目罢了。ps：谁会没事儿给网站弄那么多栏目呢，我觉得只是叫法上好听罢了，显得高端。

我先把源码贴出来，然后讲解下是实现的原理。

{% highlight php %}
<?php
	public function getList(){
		$list = $this->where(array('pid'=>0))->order('ord ASC,colid ASC')->select();
		$rlist = '';
		foreach($list as $key => $val){
			$rlist .= "<table width='100%' border='0' cellspacing='0' cellpadding='2'>";
			$rlist .= "<tr onMouseMove=\"javascript:this.bgColor='#FCFDEE';\" onMouseOut=\"javascript:this.bgColor='#FFFFFF';\">";
			$rlist .= "<td class='bline' style='background-color:#FBFCE2;'><table width='98%' cellspacing='0' cellpadding='0' border='0'><tr><td width='50%'>";
			$rlist .= $val['colid'].'.'.$val['colname'].' 【子分类：'.$this->sunNum($val['colid']);
			if($this->sunNum($val['colid']) != 0){
				$rlist .= " <a href='javascript:void(0);' target='_self' id='pid".$val['colid']."' onclick=\"showHide('sun".$val['colid']."','pid".$val['colid']."');\" style='color:red'>展开</a>";
			}
			$rlist .= " / 文档：".D('Article')->getArcNumByColid($val['colid']);
			if(D('Article')->getArcNumByColid($val['colid']) != 0){
				$rlist .= " <a href='/Article/index/colid/".$val['colid']."' style='color:red'>查看</a>";
			}
			$rlist .= " / 模型：".D('Model')->getMname($val['mid'])."】";
			if($val['isshow'] == 0){
				$rlist .= "<img  src='/Public/admin/images/yincang.gif' />";
			}
			$rlist .= '</td>';
			$rlist .= "<td align='right'><a href='/article/edit/colid/".$val['colid']."'>编辑</a>|<a href='/article/addsun/selid/".$val['colid']."'>添加子分类</a>";
			//if($this->sunNum($val['colid']) == 0){
			$rlist .= "|<a href='javascript:sdel(\"/article/del/colid/".$val['colid']."\");'>删除</a>";
			//}
			$rlist .= "&nbsp; <input type='text' style='width:25px;height:20px' value='".$val['ord']."' name='ord[]'/>";
			$rlist .= "<input type='hidden' name='colordid[]' value='".$val['colid']."'/>";
			$rlist .= "</td></tr></table></td></tr>";
			$rlist .= "<tr><td colspan='2'>";
			$rlist .= "<table width='100%' border='0' cellspacing='0' cellpadding='0' style='display:none' id='sun".$val['colid']."'>";
			$rlist .= $this->getSunList($val['colid'],"&nbsp;&nbsp;");
			$rlist .= "</table>";
			$rlist .= "</td></tr></table>";
		}
		return $rlist;
	}

	//获得分类管理子列表
	private function getSunList($colid,$step){
		$row = $this->where(array('pid'=>$colid))->order('ord ASC,colid ASC')->select();
		$sunlist = '';
		
		foreach($row as $key => $val){
			$sunlist .= "<tr height='24' onMouseMove=\"javascript:this.bgColor='#FCFDEE';\" onMouseOut=\"javascript:this.bgColor='#FFFFFF';\">";
			$sunlist .= "<td class='nbline'>";
			$sunlist .= "<table width='98%' border='0' cellspacing='0' cellpadding='0'>";
			$sunlist .= "<tr><td width='50%'>";
			$sunlist .= $step.$val['colid'].'.'.$val['colname'].' 【子分类：'.$this->sunNum($val['colid']);
			if($this->sunNum($val['colid']) != 0){
				$sunlist .= " <a href='javascript:void(0);' target='_self' id='pid".$val['colid']."'onclick=\"showHide('sun".$val['colid']."','pid".$val['colid']."')\" style='color:red'>展开</a>";
			}
			$sunlist .= " / 文档：".D('Article')->getArcNumByColid($val['colid']);
			if(D('Article')->getArcNumByColid($val['colid']) != 0){
				$sunlist .= " <a href='/Article/index/colid/".$val['colid']."' style='color:red'>查看</a>";
			}
			$sunlist .= " / 模型：".D('Model')->getMname($val['mid'])."】";
			if($val['isshow'] == 0){
				$sunlist .= "<img  src='/Public/admin/images/yincang.gif' />";
			}
			$sunlist .= '</td>';
			$sunlist .= "<td align='right'><a href='/article/edit/colid/".$val['colid']."'>编辑</a>|<a href='/article/addsun/selid/".$val['colid']."'>添加子分类</a>";
			//if($this->sunNum($val['colid']) == 0 ){
			$sunlist .= "|<a href='javascript:sdel(\"/article/del/colid/".$val['colid']."\");'>删除</a>";
			//}
			$sunlist .= "&nbsp; <input type='text' style='width:25px;height:20px' value='".$val['ord']."' name='ord[]'/>";
			$sunlist .= "<input type='hidden' name='colordid[]' value='".$val['colid']."'/>";
			$sunlist .= "</td></tr></table></td></tr>";
			$sunlist .= "<tr><td>";
			$sunlist .= "<table width='100%' border='0' cellspacing='0' cellpadding='0' style='display:none' id='sun".$val['colid']."'>";
			$sunlist .= $this->getSunList($val['colid'],$step."&nbsp;&nbsp;");
			$sunlist .= "</table></td></tr>";
		}
		return $sunlist;
	}

	//子分类数量
	public function sunNum($colid){
		$num = $this->where(array('pid'=>$colid))->count();
		return $num;
	}
?>
{% endhighlight %}

代码贴的挺多，千万不要被这么多的代码给虎了，我在显示分类的时候用php拼接生成了前台所需的html，我觉得用php来控制前台需要展现什么样的html更为方便。在RUIBlog中分类的表需要和其他的一些表进行关联什么的，在字段上多了一些，其用递归实现无限分类只用到三个字段就可以了，如下：

> id:主键
> name:分类名称
> parent:上级分类id

注意一下：顶级分类的parent一律为"0"。

第一个方法，也就是getList，它就是用来调用分类中的顶级分类的，条件一律都是parent=0；根据方法名，getSunList方法是调用子分类的，其实使用递归的地方也在此方法中，这个方法有两个参数$colid和$step，第一个参数是用于条件查询的，即parent=$colid的值，而$step则是下级分类往后空的空格，这样在显示时更为的直观。

大致的实现原理如下：

根据条件调用出顶级分类，然后做循环，使用每个顶级分类的id去找子分类(即调用getSunList方法)，在getSunList方法里面使用迭代，这样就可以很方便的找出每个子分类下面的子分类了。

大家仔细阅读以下代码，是不是体会到递归的神奇了，递归是函数是编程的一个例子，函数式编程的优点之一就是代码简短。

码字不多，还望大家谅解，有不明白的地方大家可以留言，我们可以互相学习一下！最后附上一张分类管理的图片。

![ruiblog_category_1](http://blog.zhangenrui.cn/ruiblog_category_1.png)