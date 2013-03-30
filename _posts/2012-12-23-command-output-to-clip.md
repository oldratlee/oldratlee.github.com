---
layout: post
title: 拷贝复制命令行输出放在系统剪贴板上
location: Hangzhou
permalink: /post/2012-12-23/command-output-to-clip
write-time: 2012-12-23 14:28
tags:
- 剪贴板
- 命令
- Windows
- Mac
- Linux
- Clip
- Command Line
- shell
- Unix/Linux
- xsel
- pbcopy
- copy
- paste
- 复制
- 粘贴
- 拷贝
- CTRL + C
- CTRL + V
---

为什么要这么做？
====================

* 直接把命令的输出（比如grep/awk/sed/find或是你的程序输出结果）放到剪切板上，这么就可以在IM中CTRL + V粘贴后发出去。  
避免操作的繁琐和跳跃：把结果输出到文件、用文本编辑器打开文件、选中文本、CTRL + C。
* 通过命令将文件内容拷贝到剪切板，以避免拷贝错误、操作的跳跃（跳到文件编辑器）

Windows下
=====================

使用系统自带的`clip`命令。  
\# 位于`C:\Windows\system32\clip.exe`。

示例：

```bash
echo Hello | clip 
# 将字符串Hello放入Windows剪贴板

dir | clip
# 将dir命令输出（当前目录列表）放入Windows剪贴板

clip < README.TXT   
# 将readme.txt的文本放入Windows剪贴板

echo | clip 
# 将一个空行放入Windows剪贴板，即清空Windows剪贴板
```

Linux下
=====================

使用`xsel`命令。

示例：

```bash
cat README.TXT | xsel
cat README.TXT | xsel -b # 如有问题可以试试-b选项
xsel < README.TXT 
# 将readme.txt的文本放入剪贴板

xsel -c
# 清空剪贴板
```

Mac下
=====================

使用`pbcopy`命令。
\# 对应有个`pbpaste`命令。

示例：

```bash
echo 'Hello World!' | pbcopy
# 将字符串Hello World放入剪贴板
```

最佳实践
====================

要复制结果又想看到命令的输出
---------------------------------

命令的结果输出时，如果给复制命令（即上面提到的命令clip、xsel、pbcopy）那么命令输出就看不到了。如果你想先看到命令的输出，可以下面这么做。

```bash
$ echo 'Hello World!' | tee tmp.file.txt
Hello World!
$ xsel < tmp.file.txt
$ rm tmp.file.txt
```

即先使用`tee`命令把输出输到控制台和一个文件中。

命令执行完成后，再把输出的内容放到剪贴板中。

复制SSH的公有KEY
---------------------------------

使用下面的命令：

```bash
$ pbcopy < ~/.ssh/id_rsa.pub
```

注：不同系统使用不同的复制命令

避免用文本编辑器打开这个文件、选中文本、CTRL + C这样繁琐操作。

参考资料
=====================

* [Windows下把命令行结果存放在剪贴板](http://www.cnblogs.com/mattmonkey/archive/2011/08/20/2301554.html "Windows下把命令行结果存放在剪贴板")
* [xsel(1) - Linux man page](http://linux.die.net/man/1/xsel "xsel(1) - Linux man page")
* [命令行下可直接用pbcopy命令将文件内容拷贝到剪切板以避免拷贝错误](http://www.worldhello.net/gotgithub/02-join-github/010-account-setup.html#id4 "命令行下可直接用pbcopy命令将文件内容拷贝到剪切板以避免拷贝错误")
* [pbpaste & pbcopy in Mac OS X (or: Terminal + Clipboard = Fun!)](http://langui.sh/2010/11/14/pbpaste-pbcopy-in-mac-os-x-or-terminal-clipboard-fun/ "pbpaste & pbcopy in Mac OS X (or: Terminal + Clipboard = Fun!)")

后记
=====================

还在用的Windows、吐槽弱暴命令行cmd的程序猿们，推荐使用[cygwin](http://www.cygwin.com/ "cygwin")，可以看看我的博文[惊艳的cygwin——Windows下的Linux命令行环境](http://oldratlee.com/post/2012-12-22/stunning-cygwin "惊艳的cygwin——Windows下的Linux命令行环境")。

![把命令行输出放在系统剪贴板上](http://m2.img.libdd.com/farm5/2012/1223/15/5B935348AF09C114763B5E8BFB713E5A4E76E375D0EC5_280_321.JPEG)