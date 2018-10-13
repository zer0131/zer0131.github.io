---
layout: post
title: git入门
category: linux
description: 闲来无事，总结下git的常用操作，供大家参考。
tags: git 命令
---

* content
{:toc}

闲来无事，总结下git的常用操作，供大家参考。

![git logo](http://blog.zhangenrui.cn/git-logo.png)

<!--more-->

## 安装

- [Mac OSX版](http://git-scm.com/download/mac)

- [Windows版](http://git-scm.com/download/win)

- [Linux版](http://git-scm.com/download/linux)

## 创建新仓库

> `git init`

## 克隆仓库

> - 创建一个本地仓库的克隆版本：
> 
>   `git clone /path/to/repository`
>
> - 克隆远程仓库：
> 
>   `git clone username@host:/path/to/repository`

## 配置

### 设置用户名

```
git config user.name xxxxx
```

PS：全局配置加上**--global**

### 设置邮箱

```
git config user.email xxx@xxx.com
```

### 设置记住用户密码

```
git config --global credential.helper store
```

## 工作流程
1. 工作目录：持有实际文件
2. 缓存区（Index）：临时保存改动
3. HEAD：指向你最近一次提交后的结果

![git work](http://blog.zhangenrui.cn/git_work.png)

## 添加与提交

> 1. 添加改动至缓存区：
>
>    `git add <filename>` or `git add *`
> 2. 提交至HEAD：
> 
>    `git commit -m "代码提交信息"`

## 推送改动

> 推送到远程仓库：
> 
> `git push origin master`

## 分支

分支是用来将特性开发绝缘开来的。在你创建仓库的时候，master 是“默认的”。在其他分支上进行开发，完成后再将它们合并到主分支上。

![git branch](http://blog.zhangenrui.cn/git_branch.png)

> 创建dev分支并且换：
> 
> `git checkout -b dev`
>
> 从远程分支迁出至本地
>
> `git checkout -b <local-branch-name> origin/<remote-branch-name>`
>
> 切换回master：
> 
> `git checkout master`
>
> 删除dev分支：
> 
> `git branch -d dev`
>
> 推送至远程分支：
> 
> `git push origin <branchname>`
> 
> 删除远程分支：
> 
> `git push origin :<branchname>`

## 更新与合并

> 从远程master更新本地仓库：
> 
> `git pull origin master`
>
> 合并分支至当前分支：
> 
> `git merge [options] <branchname>`

## 标签

> 创建注释标签：
> 
> `git tag -a <tagname> -m "my tag"`
> 
> 提交tag至远程：
> 
> `git push origin <tagname>`
> 
> 删除tag：
> 
> `git tag -d <tagname>`
> 
> 删除远程tag：
> 
> `git push origin :refs/tags/<tagname>`


##  替换本地改动

> 使用HEAD中的最新内容替换掉你的工作目录中的文件：
> 
> `git checkout -- <filename>`
>
> 丢弃你所有的本地改动与提交，可以到服务器上获取最新的版本并将你本地主分支指向到它：
> 
> `git fetch origin` and `git reset --hard origin/master`

## 一些参考资源

- [git文档](http://git-scm.com/doc)

- [git社区](http://git-scm.com/community)

- [Pro Git（中文版）](http://git.oschina.net/progit/)
