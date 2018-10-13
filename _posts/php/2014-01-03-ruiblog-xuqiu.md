---
layout: post
title: RUIBlog开发手记之需求
category: php
keywords: RUIBlog,PHP
tags: RUIBlog PHP ThinkPHP 需求
---

* content
{:toc}

## 写在前面的话

我又来写文章啦同志们，其实我觉得关于RUIBlog还有很多地方需要改进，不过这个是后话，先把前面我在开发时用的技术和一些感悟做个总结吧！

我想了想，为了专业化，今儿就写写关于RUIBlog的需求分析吧。我最初的想法是想用ThinkPHP做个功能完善的CMS系统，可以满足中小型网站的建设，就算做事出的一个产品吧！后来，我想了想，还是先做个博客系统，然后再在博客系统的基础上做CMS系统，我个人觉得，在需求上博客系统和CMS系统是差不多的，只是有些功能点不太一样罢了。

<!--more-->

## 需求分析

1、实现文章管理，如新增，编辑、删除、文章审核、文章移动等。

2、实现分类管理，基本的增删改查不用多说，还有分类中添加子分类，分类和文章之间的关系。

3、文章标签管理，实际上就是对文章中关键字的统计，在前台中可以增强显示。

4、单页和模型管理我暂时不多说，我做这个是为了后期做CMS系统做准备的。

5、评论，即管理文章中的评论，管理员回复功能，评论和文章之间的关系都是不能缺少的。

6、留言较评论简单些，除了没有和文章之间的关系，其他基本上一致。

7、友情链接，分图片友情链接和文字友情链接，在RUIBlog前台中主要用文字友情链接。

8、用户管理，是对后台登录用户的管理，系统会创建时间、登录时间、登录IP以及发布的文章等。

9、角色管理，使用THinkPHP中的RBAC实现的，具体做法后期我会写专门的文章。

10、节点管理，它也是RBAC中的一部分。

11、系统设置，不和数据库打交道，是以php文件的形式保存，主要用于对前台的一些配置。

12、清理缓存，其实就是删除前后和后台中Runtime文件中的东西，这个文件夹保存ThinkPHP在工作时产生的缓存文件。

13、数据备份和还原，备份数据库中的记录以.sql文件保存，数据还原可以将.sql文件中的记录还原到数据库；主要是用于网站的备份和搬家。

我大概总结了一下，说起来在功能上并不是很丰富，更多的功能我会在后期慢慢开发和完善。

## 表结构

1、iqishe_article：文章表

2、iqishe_columns：栏目表又叫分类表

3、iqishe_singlepage：单页表

4、iqishe_model：模型表

5、iqishe_comment：评论表

6、iqishe_feedback：留言表

7、iqishe_links：友情链接表

8、iqishe_users：用户表

9、iqishe_tags：标签表

10、iqishe_access：权限表

11、iqishe_node：节点表

12、iqishe_role：角色表

13、iqishe_role_user：角色用户关系表

注：10-13为RBAC中使用的表。

关于详细的表结构，我写在word中，大家可以点下面的链接下载

[http://pan.baidu.com/share/link?shareid=2388552896&uk=1846304022](http://pan.baidu.com/share/link?shareid=2388552896&uk=1846304022)

## 文件结构

inde.php---前台入口文件

admin.php---后台入口文件，可自行修改

robots.txt---搜索引擎蜘蛛抓取配置文件

readme.txt---这个和功能无关，不过你可以看看

.htaccess---用于Apache服务器的url重写的文件

favicon.ico：标题图标

Admin---后台文件夹

Home---前台文件夹

Public---公共文件夹

backupdata---存放数据库备份文件夹

Uploads---上传文件保存文件夹

install---安装文件夹，安装后可删除

ThinkPHP---ThinkPHP核心文件夹

![ruiblog_xuqiu_1](http://blog.zhangenrui.cn/ruiblog_xuqiu_1.jpg)