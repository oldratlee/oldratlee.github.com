---
layout: post
title: Windows命令行CMD的使用小技巧
location: Hangzhou
permalink: /319/tech/shell/windows-cmd-usage-tips.html
write-time: 2010-12-12 21:44
tags:
- Shell
- cmd
- windows
- tips
---

Windows下的命令行CMD比起Linux下用户体验差了太多，对于常常要在命令行操作的程序员来说，很是痛苦。

下面列出一些CMD使用的小技巧，希望能给你带来一些便利。 8)

一、好用的快捷键
======================

**F7**：打开命令历史窗口。通过上下分键来选择，或是通过字母键来循环选择。

![](/files/windows-cmd-usage-tips_1.jpg)

**F8**：键入部分命令，按F8键补全，是从历史中选出与输入部分相同的命令，如果有多个则反复按F8循环选择。

二、使用鼠标右键来选中、复制粘贴
=====================

缺省，选中、复制粘贴操作要使用右键菜单，比较麻烦。

通过开启【快速编辑模式】来启用

![](/files/windows-cmd-usage-tips_2.PNG)

三、更换命令提示符
=============================

缺省提示符包含了当前路径，可以通过cmd的参数来修改。

在快捷方式的中添加

{% highlight bash linenos %}
%windir%\system32\cmd.exe /k "ver & prompt $N:$$$S"
{% endhighlight %}

把提示符修改成 `盘符+:+$+空格`。

![](/files/windows-cmd-usage-tips_3.PNG)