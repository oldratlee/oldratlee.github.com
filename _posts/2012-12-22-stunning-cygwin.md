---
layout: post
title: 惊艳的cygwin——Windows下的Linux命令行环境的配置和使用
location: Hangzhou
permalink: /post/2012-12-22/stunning-cygwin
write-time: 2012-12-22 13:46
tags:
- shell
- Unix/Linux
- cmd
- Windows
- 命令行
- cygwin
- 163
- git
- 自动补全
- Command Line
- 丑小鸭
- 天鹅
- 权限
- 配置
---

5年前倒腾过一次cygwin，当时体验感觉不好。到现在一直用的是[GNU utilities for Win32](http://unxutils.sourceforge.net/ "GNU utilities for Win32")，在Windows的CMD中使用*nix的命令工具包。

GNU utilities for Win32很久没有更新，utils的版本太低，很多功能没有（比如grep输出不支持彩色输出的选项--color）等等。另，在Windows的“cmd的自动补全”、“命令历史”、“bat编程”太弱，一直忍受着~  
\# 以前写过cmd相关的博文： [Windows命令行CMD的使用小技巧](http://oldratlee.com/319/tech/shell/windows-cmd-usage-tips.html "Windows命令行CMD的使用小技巧")、[如何在Windows下使用GNU命令](http://oldratlee.com/297/tech/unix/use-unix-command-under-windows.html "如何在Windows下使用GNU命令")

最近大半年版本管理使用[Git](http://zh.wikipedia.org/zh-cn/Git "Git")，用的是[msysgit](http://code.google.com/p/msysgit/ "msysgit")。msysgit带了[MSYS](http://www.mingw.org/wiki/MSYS "MSYS")的Bash。用的过程中体验不错，很顺畅很有Linux的Feel了：

* 有Git命令的自动补全
* 彩色显示
* 可以用Bash功能（自动补全、历史命令搜索等等）
* bash脚本编程

昨天想到就倒腾了一下[cygwin](http://cygwin.com/ "cygwin")，效果惊艳啊！

安装
======================

在cyginw的[安装页面](http://cygwin.com/install.html)上下载setup.exe。

启动选择安装目录和Package的镜像站点（**自动**会缺省163的Package镜像站点），然后选择和调整要安装的Package。  
\# [163](http://www.163.com/ "163")提供的镜像让Package下载分分钟搞定。163也提供了Ubuntu的镜像，163做得很赞！

安装后要调整（比如新增、删除）也使用setup来完成。

Package中有Git，需要就在安装时选上就可以了。

bash_completion包（命令补全的增强包）让cygwin补全更强劲，但会影响命令行自动补全速度和cygwin启动速度，建议先安装上，如果不能接受速度的变慢，就卸载掉这个包。  
注：参见 [有关 SVN、CYGWIN 和 NOTEPAD++](http://blog.solrex.org/articles/about-svn-cygwin-and-notepad.html)

第一次启动时会生成**Home目录**，在${cygwin}/home/${YOUR\_USER\_NAME}。  
\# ${cygwin}表示你的cygwin安装目录。

显示
====================

调整${HOME}/.bashrc文件，把注释掉别名打开：

{% highlight bash %}
alias df='df -h'
alias du='du -h'

alias whence='type -a'                        # where, of a sort
alias grep='grep --color'                     # show differences in colour
alias egrep='egrep --color=auto'              # show differences in colour
alias fgrep='fgrep --color=auto'              # show differences in colour

alias ls='ls -h --color=tty'                 # classify files in colour
alias dir='ls --color=auto --format=vertical'
alias vdir='ls --color=auto --format=long'
alias ll='ls -l'                              # long list
alias la='ls -A'                              # all but . and ..
alias l='ls -CF'                              #
alias wch='which -a'
{% endhighlight %}

这样调整后，可以ls、grep、dir输出彩色显示。

另外加上命令的-h选项，这样文件大小以K、M、G显示，方便人阅读。

git输出（比如log、status）彩色显示，使用下面的命令配置：

{% highlight bash %}
git config --global color.ui auto
{% endhighlight %}

vi配置
======================

在`${HOME}/.vimrc`文件中加上：
\# 没有`.vimrc`文件就新建。

{% highlight bash %}
set number
set hlsearch
set fileencoding=utf-8
set fileencodings=ucs-bom,utf-8,cp936,gb18030,big5,euc-jp,euc-kr,latin1

set nocompatible
set backspace=indent,eol,start

syntax enable
{% endhighlight %}

说明：

* syntax enable：打开语法高亮。cygwin的vi缺省没有打开。
* set nocompatible和set backspace：配置backspace键，缺省backspace不起作用。
* set fileencoding和set fileencodings：缺省文件编码和自动识别文件编码顺序
* set number：显示行号
* set hlsearch：搜索到内容高亮

参考资料：

* [Cygwin中VIM的设置](http://blog.sina.com.cn/s/blog_4b9eab320100xw2a.html)
* [VIM文件编码识别与乱码处理](http://edyfox.codecarver.org/html/vim_fileencodings_detection.html)

配置盘符的链接
===================

到D盘，要`/cygdrive/d`，可以新建符号链接`/d`，这样可以减少录入（[MSYS](http://www.mingw.org/wiki/MSYS "MSYS")的做法）

{% highlight bash %}
ln -s /cygdrive/c /c
ln -s /cygdrive/d /d
ln -s /cygdrive/e /e
{% endhighlight %}

自动补全不区分大小写
===========================

`~/.bashrc`文件中添加：

{% highlight bash %}
shopt -s nocaseglob
{% endhighlight %}

`~/.inputrc`文件中添加：

{% highlight bash %}
set completion-ignore-case on
{% endhighlight %}

cygwin的官方文档：[How can I get bash filename completion to be case insensitive?](http://cygwin.com/faq/faq-nochunks.html#faq.using.bash-insensitive)

配置按单词移动/删除
==================

`.inputrc`文件中添加：

{% highlight bash %}
# Ctrl+Left/Right to move by whole words
"\e[1;5C": forward-word
"\e[1;5D": backward-word

# Ctrl+Backspace/Delete to delete whole words
"\e[3;5~": kill-word
"\C-_": backward-kill-word
{% endhighlight %}

参考资料：[Ctrl-Arrow Keys, Ctrl-Backspace, Ctrl-Delete](http://www.samhartsfield.com/dokuwiki/info/cygwin)

Windows和cygwin路径的转换
==========================

cygwin的路径和Windows的路径表示不一样。

要注意的是，cygwin下的`cd`命令可以**直接使用**Windows的路径表示。

{% highlight bash %}
$ cd 'C:\Windows\System32\drivers\etc'
{% endhighlight %}
注：不要忘了加上**单引号**，因为`\`是bash元字符，用于转义。不用上单引号`cd`命令收到的参数值就不是`C:\Windows\System32\drivers\etc`，运行报错。

路径转换的需求减了大半。

有`cygpath`命令来完成转换，相关的选项是：

{% highlight bash %}
  -a, --absolute        output absolute path
  -w, --windows         print Windows form of NAMEs (C:\WINNT)
  -u, --unix            (default) print Unix form of NAMEs (/cygdrive/c/winnt)
{% endhighlight %}

执行的例子：

{% highlight bash %}
$ cygpath -au 'C:\Windows\System32\drivers\etc'
/cygdrive/c/Windows/System32/drivers/etc
$ cygpath -aw '/cygdrive/c/Windows/System32/drivers/etc'
C:\Windows\System32\drivers\etc
{% endhighlight %}

cygwin的官方文档：[How do I convert between Windows and UNIX paths?](http://cygwin.com/faq-nochunks.html#faq.using.converting-paths "How do I convert between Windows and UNIX paths?")

在cygwin的打开指定文件或文件夹到文件浏览器
=========================

常常会有这样的需求，比如打开文件浏览器`explorer`，然后用乌龟看SVN日志等等。

可以使用使用命令直接打开指定文件或文件夹的位置到`explorer`。

打开文件或文件夹脚本，可以这个脚本命名成`xpl`，放到PATH上。  
\# `xpl`是`explorer`的缩写

{% highlight bash %}
#!/bin/bash

cygwin=false;
case "`uname`" in
    CYGWIN*) cygwin=true ;;
esac

if [ "$1" = "" ]; then
	XPATH=. # 缺省是当前目录
else
	XPATH=$1
	if $cygwin; then
		XPATH="$(cygpath -C ANSI -w "$XPATH")";
	fi
fi

explorer $XPATH
{% endhighlight %}

打开文件或文件夹，并选中的脚本，可以这个脚本命名成`xpf`，放到PATH上。  
\# `xpf`是`explorer and select file`的缩写

{% highlight bash %}
#!/bin/bash

cygwin=false;
case "`uname`" in
    CYGWIN*) cygwin=true ;;
esac

if [ "$1" = "" ]; then
	XPATH=. # 缺省是当前目录
else
	XPATH=$1
	if $cygwin; then
		XPATH="$(cygpath -C ANSI -w "$XPATH")";
	fi
fi

explorer '/select,' $XPATH
{% endhighlight %}

文件权限问题
======================

现象
--------------

Windows的文件的cygwin下没有权限：

{% highlight bash %}
$ rm foo.txt
error: open("foo.txt"): Permission denied
error: unable to index file foo.txt
$ ll foo.txt
----------+ 1 Jerry None 486 Dec 24 14:16 foo.txt
{% endhighlight %}

文件的权限显示的是`----------+`，没有读写的权限。

解决方法
------------

编辑`/etc/fstab`，在末尾加上下面的一行：   

{% highlight bash %}
none /cygdrive cygdrive binary,noacl,posix=0,user 0 0
{% endhighlight %}

关闭所有cygwin进程，再重启cygwin命令行。

显示文件权限已经正常`-rw-r--r--`：

{% highlight bash %}
$ ll foo.txt
-rw-r--r-- 1 Jerry None 486 Dec 24 14:16 foo.txt
{% endhighlight %}

*注意！* 如果改了`/etc/fstab`但是没有生效，可以重启一下机器！

参考资料： [cygwin sets file permission to 000](http://stackoverflow.com/questions/5828037/cygwin-sets-file-permission-to-000)

Windows命令的乱码
======================

Windows命令的输出中文乱码，原因是Windows命令输出的编码是`GBK`。cygwin控制台`mintty`的编码缺省是`UTF-8`。`mintty`的选项的【Text】把编码改成`GBK`即可。

参见：本文“文本配置：字体、编码”一节的截图。

命令窗口设置：字体、右键粘贴等等
===============================

这些设置对使用的舒适度至关重要。

cygwin的执行文件是`mintty.exe`，在命令窗口的标题的右键菜单上有【options】项，有这些配置项。

外观
--------------------

![外观](http://m3.img.libdd.com/farm5/2012/1222/14/6598BF166F0BAC4FB928E1C05BF7B12A84E77CE8D53B5_400_285.PNG)
配置光标显示、窗体透明。

文本配置：字体、编码
--------------------

![字体](http://m1.img.libdd.com/farm4/2012/1223/11/27AC746CC0F02E1F3FC5E487A9E3EF6B4D3A799A7B948_400_285.PNG)
配置显示字体。

我喜欢用`Consolas`字体，这是Windows上一款质量很高的等宽字体。

右键粘贴配置
--------------------

![右键粘贴配置](http://m2.img.libdd.com/farm5/2012/1222/14/131F0E35F9DDC37D1E84F738D943CF23BB9FEE35F8E75_400_285.PNG)
配置右键用于粘贴，缺省是弹出菜单。这个配置很方便！

效果图
=================================

![效果图](http://m2.img.libdd.com/farm5/2012/1222/14/25349F08D1AD96A31D21A4372CEBC17E058A625D92FBE_475_328.PNG)

展示了ls、grep输出的彩色显示，容器的字体效果。

vi的语法高亮就不再截图了。

一些最佳实践
=====================

[把命令行输出放在系统剪贴板上](http://oldratlee.com/post/2012-12-23/command-output-to-clip "把命令行输出放在系统剪贴板上")

后记
======================

之前对cygwin这种适配的做法有偏见，觉得做得不会好。其实有了一个好点子，又有为之努力的人在，就会越来越好，从“丑小鸭”变成***惊艳***的“天鹅”。

谢谢这些为之不懈努力的人！

有了cygwin，让我对MacBook Pro的需求程度降低了，在Windows上还可以再呆一下子。 :)

OS作为一个工作环境，帮我方便的完成要做的事，关注点是：

* 方便的大量的软件。Windows这一点太强。
* 高质量的UI。Linux这一点太差。
* 程序员要的舒适的命令行环境。Windows的cmd太弱，有了cygwin可顶一下。

相关资料
=================
* [cygwin官方文档](http://cygwin.com/cygwin-ug-net/cygwin-ug-net.html "cygwin官方文档")
* [Msys/MinGW与Cygwin/gcc](http://zengrong.net/post/1723.htm "Msys/MinGW与Cygwin/gcc")
* [MinGW，MSYS，cygwin区别](http://hi.baidu.com/zyqhi2010/item/feb5f4615554719bc5d249b4)
* [Cygwin与MinGW/MSYS，如何选择？](http://zengrong.net/post/1557.htm "Cygwin与MinGW/MSYS，如何选择？")
* [PuTTYcyg的替代者mintty](http://zengrong.net/post/1553.htm "PuTTYcyg的替代者mintty")
* [使用Git、Git GUI和TortoiseGit](http://zengrong.net/post/1722.htm "使用Git、Git GUI和TortoiseGit")
* [git乱码解决方案汇总](http://zengrong.net/post/1249.htm "git乱码解决方案汇总")
* [在cygwin中调用JAVA程序](http://zengrong.net/post/1610.htm "在cygwin中调用JAVA程序")