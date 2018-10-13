---
layout: post
title: Sublime Text 2的汉化和破解
category: technology
keywords: SumlimeText
description: Sublime Text 2的汉化和破解
tags: Sublime 破解 汉化
---

* content
{:toc}

![sublime_text](http://blog.zhangenrui.cn/sublime_text.png)

## 前面的废话

今天有些小高兴吧，看到了一个新的编辑器，名曰：Sublime Text 2，很多网友都说这个是程序员必备利器，安装了一下，感觉还是很不错的哇！！！遗憾的是这货收费的，无奈之下只好去搞什么破解了(哎，天朝程序员的悲哀...)。废话不多说直接说过程吧。

<!--more-->

> [百度云下载Mac版(2.0.2)](http://pan.baidu.com/s/1hqETLm0)

> [百度云下载Win集合版](http://pan.baidu.com/s/1jGEPHVgs)

## 破解

首先，你得下载一个Sublime Text，以下是网址：[http://www.sublimetext.com/](http://www.sublimetext.com/) 当然如果你不想在官网下载，你可以往下看。然后就是我们常说的注册机什么的了，这货其实还蛮高端的，以下是使用方法：

### 1、关闭Sublime Text 2

### 2、解压下载后的附件，打开注册机，Win7需要用管理员权限运行！

### 3、按下图步骤完成：

![sublime_text_1](http://blog.zhangenrui.cn/sublime_text_1.jpg)

![sublime_text_2](http://blog.zhangenrui.cn/sublime_text_2.jpg)

第一步找到“Generate”后，把License全选并且复制，然后点击第二步“Patch Key”，找到安装目录，选择主程序。提示成功后打开Sublime Text 2，在菜单里找到“help”，“enter license”，把刚才复制的粘贴进去就ok了！

### 4、完成上述，就应该破解成功了

![sublime_text_3](http://blog.zhangenrui.cn/sublime_text_3.jpg)

啊哈，现在就已经是注册过的了，你可以随心所欲的写代码了！！！

### 5、可以复制下面的注册码

----- BEGIN LICENSE -----

Andrew Weber

Single User License

EA7E-855605

813A03DD 5E4AD9E6 6C0EEB94 BC99798F

942194A6 02396E98 E62C9979 4BB979FE

91424C9D A45400BF F6747D88 2FB88078

90F5CC94 1CDC92DC 8457107A F151657B

1D22E383 A997F016 42397640 33F41CFC

E1D0AE85 A0BBD039 0E9C8D55 E1B89D5D

5CDB7036 E56DE1C0 EFCC0840 650CD3A6

B98FC99C 8FAC73EE D2B95564 DF450523

------ END LICENSE ------

## 汉化

如果你想练习英文什么的，你可以不用汉化，鉴于对这个编辑器的不熟悉，我个人还是决定汉化了，那么就往下看吧。

汉化方法：解压下载后的附件，解压汉化包到系统盘中Sublime Text 2的"packages/Default"目录下，覆盖原始文件。这样就汉化完成了。

提示：Packages路径，可通过 Sublime Text 菜单中的 Preferences > Browse Packages 找到 *Packages* 目录。

## 安装Package Control

### 1、点击菜单栏 Preferences > Browse Packages…

### 2、找到Installed Packages文件夹，并将安装包拷贝进去。

### 3、打开控制台，即console，复制下面代码并运行

**import urllib2,os,hashlib; h = 'eb2297e1a458f27d836c04bb0cbaf282' + 'd0e7a3098092775ccb37ca9d6b2e4b7d'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); os.makedirs( ipp ) if not os.path.exists(ipp) else None; urllib2.install_opener( urllib2.build_opener( urllib2.ProxyHandler()) ); by = urllib2.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); open( os.path.join( ipp, pf), 'wb' ).write(by) if dh == h else None; print('Error validating download (got %s instead of %s), please try manual install' % (dh, h) if dh != h else 'Please restart Sublime Text to finish installation')**

> 注意：在3下面请使用下面的脚本：

**import urllib.request,os,hashlib; h = 'eb2297e1a458f27d836c04bb0cbaf282' + 'd0e7a3098092775ccb37ca9d6b2e4b7d'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)**