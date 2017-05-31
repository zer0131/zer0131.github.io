---
layout: post
title: Mac PHPStorm 中文符号无法输入
category: php
keywords: Mac,PHPStorm,中文输入Bug
description: Mac中PHPStorm10无法输入中文符号的处理办法
tags: 问题 MacOSX PHPStorm
---

* content
{:toc}

本文记录问题在 Mac OS X 10.10 上验证，其他 *nix 用户可作参考，Mac OS X 平台上的 JetBrains 其它产品（如 WebStorm、IntelliJ IDEA）亦可参考。Windows 用户可能不存在这些问题。

## 文字输入、显示问题

### PHPStorm 内置 JDK 版无法输入中文标点？

PHPStorm 10 提供了内置 JDK 1.8 的版本，用于提升性能，解决 Apple 官方提供的 Java（1.6） 过于陈旧问题。但是这个版本存在中文标点输入以后自动被转换成英文（半角）的 Bug，把 PHPStorm 集成的 OpenJDK，换成 Oracle 官方 8u51 版本以后，问题仍然存在。

<!--more-->

经试验，使用 JDK 8u45 可以正常输入中文标点，具体如下：

- 下载安装 JDK 8u45 （如果以前安装过更新版本，先卸载掉）；

- 删除 PHPStorm 内置的 JDK： /Applications/PHPStorm/Contents/  里面的 jre 文件夹；

- 重启 PHPStorm 搞定。

当然使用 JDK 1.6、1.7 都能正常输入中文标点，但基于性能考虑，建议使用 JDK 1.8。

**注意：**
内置 JDK 的版本提供了选择 JDK 的功能，按 CMD + Shift + A ，输入 JDK ，选择 Switch IDE boot JDk... 可以选择 JDK ，但即便选择系统安装在系统的 JDK，重新启动以后 JVM 仍然是内置的，于事无补

## 字体、颜色渲染问题

### 主题渲染出来的颜色比指定的颜色浅？

使用 JetBrains 系列编辑器时会发现，在诸如 Sublime 之类的编辑器里很炫的主题，在 JetBrains 编辑器里渲染出来像劣质牛仔水洗后褪色一样，很戳眼，比如这个帖子中的例子：指定 #292929 背景色渲染出来是 #363636 。太扯了！
问题出在哪呢？JDK！没错，就是它！又是它！

很多 Mac 用户安装的是苹果官方提供的 JRE，古老的 1.6，而且看架势，即便 OS X 10.11 El Capitan 发布以后，苹果也不会提供新的版本。

还是自力更生吧，两种方法：

- 方法一：使用内置 JDK 的版本：近期更新的软件 JetBrains 官方都提供了内置 JDK 的版本（然后呢，就遇到了上面的问题，你知道该怎么做了）；

- 方法二：从 Oracle 下载安装新版 JDK 8u45（为什么不是最新版？看上面的问题），然后修改 PHPStorm 启动参数：打开  /Applications/PhpStorm.app/Contents/Info.plist ，搜索 JVMVersion ，修改为：

{% highlight xml %}
<key>JVMVersion</key> 
<string>1.8*</string> 
{% endhighlight %}

重新启动以后，精心调教的主题终于不偏了。

### 字体渲染问题

- 使用 Source Code Pro 字体以后会[出现一堆框框]？([http://sticksnglue.com/wordpress/source-code-pro-phpstorm-the-plot-thickens/](http://sticksnglue.com/wordpress/source-code-pro-phpstorm-the-plot-thickens/))

- 字体在外接显示器上渲染出来像非洲难民一样惨不忍睹？

这都是拜 JDK 1.6 所赐，解决方法：升级到 JDK 1.8（见上），然后打开  **/Applications/PhpStorm.app/Contents/bin/phpstorm.vmoptions** ，在末尾添加下面几行并保存，重启编辑器以后应该顺眼一些了。

```
Dawt.useSystemAAFontSettings=gasp
Dswing.aatext=true
Dsun.java2d.xrender=true
```

**参考链接：**

- [How to fix font anti-aliasing in IntelliJ IDEA when using high DPI?](http://superuser.com/questions/614960/how-to-fix-font-anti-aliasing-in-intellij-idea-when-using-high-dpi)

- [Java Runtime Environment Fonts](https://wiki.archlinux.org/index.php/Java_Runtime_Environment_Fonts)

如果想继续使用 JDK 1.6，可以执行以下操作以改进字体渲染：

- 打开 **/System/Library/Java/JavaVirtualMachines/1.6.0.jdk/Contents/Home/lib/fonts/** 文件夹；

- 把 Source Code Pro 或者其它想使用的字体拷进去，重启编辑器以后到设置里面选择即可。

## 其他问题

### 有时会卡顿？

- 提高最大内存限制：

- 打开  **/Applications/PhpStorm.app/Contents/bin/phpstorm.vmoptions** ， **-Xmx750m** 修改为 **-Xmx2048m** 或者更大的值。

- 减少不必要的文件索引，如构建输出文件夹等：设置中 Project -> Directories 点击不需要索引的目录，然后点击 EXcluded ，更多细节参见官方文档。

### 推荐主题

- [Solarized Colorscheme for IntelliJ IDEA](https://github.com/jkaving/intellij-colors-solarized)
- [Oceanic Next for JetBrains IDE](https://github.com/minwe/oceanic-next-jetbrains)

## 总结

JetBrains 家族的编辑器功能强大、紧随生态圈的流行技术，加上跨平台性，赢得了越来越多开发者青睐。然成也萧何，败也萧何。JetBrains 系列编辑器深受 Java 拖累，经常因为一些很基本的性能、渲染问题遭诟病。使用过程中遇到的很多问题都可能追溯到 JDK（JRE）上，大概是受国内很多写的很烂的 JSP 网站影响，听到 Java 总想侧目。

从 PHPStorm 到 WebStorm，三、四年的使用下来，JetBrains 系成了我最喜欢的 IDE。爱之深，痛之切，有时会幻想：如果用更高级的系统级语言开发，以获得更好的性能和体验，那该多好……但毕竟是商业软件，为性能和体验放弃一些平台、提高开发成本并不合商业逻辑。我们当然期待更多的提升和改进，不过在不差 SSD 和内存的今天，维持现状也还行。