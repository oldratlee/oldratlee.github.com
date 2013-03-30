---
layout: post
title: 如何在Windows下使用GNU命令
location: Hangzhou
permalink: /297/tech/unix/use-unix-command-under-windows.html
write-time: 2010-12-12 18:10
tags:
- gnu
- command
- Shell
- windows
- Unix/Linux
- uinx
---

Unix/Linux系统下命令比起Windows要系统完整的多。

这些命令用起来真是其乐无穷啊~ :)

- grep
- find
- sort
- awk
- sed
- wc
- less / more
- head / tail
- cat / tac
- tee
- xargs
- ……

在Unix/Linux下使用后再到Windows下，Windows下的cmd相形见绌，实在是不爽。既然这是个问题，总会有人注意到，去解决。

这些GNU命令在Window下是有Port的。在Windows下使用GNU命令有多种方案可选。

一、使用命令的Native的Port
=================================

我偏爱这种方式，在Windows的Cmd中使用命令。

1. 下载和配置
-------------------

Native命令下载地址在：  
<http://unxutils.sourceforge.net>

上面列出有两个下载：  
<http://unxutils.sourceforge.net/UnxUtils.zip>  
<http://unxutils.sourceforge.net/UnxUpdates.zip> 一些更新的命令    
\# 原始地址可能已经不能下载，但是使用迅雷是可以下载的（会从镜像站下载）

把UnxUtils.zip解压到一个目录下，比如`D:\bin\UnxUtils`。

Native命令位于D:\bin\UnxUtils\usr\local\wbin 目录下，把这目录加到PATH环境变量中，这们大功告成，重启Cmd可以用了。

UnxUpdates.zip中更新的命令，直接覆盖D:\bin\UnxUtils\usr\local\wbin目录下。

PS:  
可以把路径D:\bin也加到环境变量PATH中，这个路径下可以放自己的命令或是批处理（BAT）文件。比如下文提到的dsb.bat、xpl.bat、xpf.bat 等等。

2. 要调整的问题
-------------------

用的过程中你会发现

- date
- echo
- find

三个命令使用的不是Unix Port的那个，原因是

- date、echo是Cmd的内部命令。
- find在C:\Windows\system32目录下已经有了一个。

我的解决方法是，把D:\bin\UnxUtils\usr\local\wbin下的这三个命令重命名，前面加上u：

- udate
- uecho
- ufind

好，来一个示例：

删除一个目录下所有的Eclipse工程文件

{% highlight bash linenos %}
ufind -iname .project -or -iname .settings | xargs rm -rf
{% endhighlight %}

3. 你可能不知道的Windows自带命令的用法
---------------------------------------

Windows下已有的命令也可以。

### explorer

即文件浏览器。

加上选项，打开文件或是文件夹并选中。

加上下面的选项(/select,)，会打开文件文件浏览器，并选中文件夹c:/windows。

{% highlight bash linenos %}
explorer /select, c:/windows
{% endhighlight %}

可以作两个批处理文件，减少键入：

xpl.bat （打开指定的文件）

{% highlight bash linenos %}
@ start "Title Placeholder" explorer %*
{% endhighlight %}

xpf.bat （打开文件或是文件夹并选中）

{% highlight bash linenos %}
@ start "Title Placeholder" explorer /select, %*
{% endhighlight %}

### dir

加上 /s /b 选项后，只会列出指定的文件。很适合用来搜索文件。

命令行搜索速度很快。

可以作一个批处理文件，减少键入：（见上图的最后一个示例）

dsb.bat

{% highlight bash linenos %}
@ dir /s /b %*
{% endhighlight %}

PS:   
有关CMD的小技巧参见我的文章《Windows的CMD使用小技巧》。

二、使用Cygwin http://www.cygwin.com/
===========================================

Cygwin通过一个中间层，模拟Linux API（cygwin1.dll）。然后把Linux的命令Port过来。

使用Cygwin提供了一个Shell。

优点

Port的命令比较全。连gcc都有。

缺点

路径是模仿Unix的，访问Windows下的文件，路径很怪，/media/c/Program files/

参照资料：
====================

- UNIX Command Line Tools For MS-Windows XP / Vista / 7 Operating Systems   
<http://www.cyberciti.biz/faq/unix-command-line-utilities-for-windows/>
- Cygwin is a Linux-like environment for Windows <http://www.cygwin.com/>
- some ports of common GNU utilities to native Win32 <http://unxutils.sourceforge.net/>
