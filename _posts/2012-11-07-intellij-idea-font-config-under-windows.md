---
layout: post
title: Windows下IntelliJ IDEA代码显示的中文字符字体配置
location: Hangzhou
permalink: /post/2012-11-07/intellij-idea-font-config-under-windows
write-time: 2012-11-07 22:52
tags:
- IntelliJ IDEA
- IntelliJ
- IDEA
- 字体
- Font
- 中文字符
- 中文
- 配置
- Java
- Windows
- netbeans
---

Windows下IntelliJ IDEA代码里的中文字符显示效果不好，大小不一、歪歪扭扭的，斜体时用字体也不一样。

一直想微调一下IDEA的字体，比如分别设置英文字符用一个字体，中文字符用另一个字体，搜索了但没有结果。

昨天想到是不是可以配置**JVM的字体**呢？一搜有了。  
\# 因为实际上调整的是JVM的字体配置，所以这种字体配置方法对NetBeans等其他Java的GUI也是有效的。

参着说明（见后面的参考资料 ），配置、重启IDEA、查看效果，再配再重启再看，倒腾了2个多小时，搞定。说明如下。

# 操作

很简单，就下面两步，放置一个文件、配置IDE字体：

1. 下载下面对应IntelliJ IDEA版本的fontconfig.properties文件，放到`${IDEA_HOME}\jre\jre\lib`目录下。${IDEA_HOME}表示IntelliJ IDEA的安装目录。  
	* **IntelliJ IDEA 11**： [fontconfig.properties](http://s.yunio.com/1R1Zrw "fontconfig.properties")
	* **IntelliJ IDEA 12**： [fontconfig.properties](http://s.yunio.com/15Hjau "fontconfig.properties")
2. IntelliJ IDEA的【File菜单】-【Settings窗口】中把代码字体设置成`Monospaced`。  
Line Space（行间距）根据自己的感觉调整。我喜欢紧凑些，设置的值是`0.75`。  
![代码字体设置](http://m1.img.libdd.com/farm4/2012/1107/22/7B2E28E24FBA46BC274D62316EBCB3B41C07EDE9ABE44_500_353.jpg "代码字体设置")

上面的配置完成后，重启IntelliJ IDEA即可。

# 效果图
![IntelliJ IDEA的中文字符显示效果图](http://m1.img.libdd.com/farm4/2012/1107/22/2277F825874C731A6572F9D4677567620E1FA429769CE_497_542.PNG "IntelliJ IDEA的中文字符显示效果图")

英文字体用的是**Consolas**，中文字体是**微软雅黑**。

# 字体配置中修改内容的说明

`fontconfig.properties`是Java的字体配置文件。
Windows下的缺省的配置是`${IDEA_HOME}\jre\jre\lib\fontconfig.properties.src`，这个文件不会生效，文件名改成`fontconfig.properties`才生效，也就是上面下载的文件的文件名。

可以Diff一下下载的`fontconfig.properties`和已有的`fontconfig.properties.src`文件。我做的修改是：

1. 添加**Consolas**和**微软雅黑**的字体名到字体文件的映射。  
就是在文件末尾的`filename.xxx`的部分。
1. 调整逻辑字体**Monospaced**引用的字体。  
字母字符的字体由`Courier New`改成`Consolas`。  
中文字符的字体设置成`Microsoft Yahei`，即微软雅黑。
1. 中文编码**字符集**顺序把`alphabetic`调整到最前面，否则会先从`chinese-ms936`**字符集**对应的微软雅黑中找到英文字母的字体了，不是等宽，代码的显示效果不好。

JVM字体配置的详细说明见参考资料 :)

# 参考资料

* [Windows下Java应用程序字体配置](http://hi.baidu.com/hzqtcbf9e/item/39e89c39df420a4b033edc8f "Windows下Java应用程序字体配置")
* [Font Configuration Files](http://docs.oracle.com/javase/1.5.0/docs/guide/intl/fontconfig.html "Font Configuration Files")

# 为什么不使用“雅黑Consolas混合字体”的方案

1. 雅黑Consolas混合字体中Consolas的斜体是不对。  
\# Consolas的正常和斜体是两个字体文件，不一样的。Consolas斜体很好看。
1. 雅黑Consolas混合字体字体行高MS也有些问题。

换句话说，雅黑Consolas混合字体质量不好。

“雅黑Consolas混合字体”的方案参见： [IDEA编辑器常用设置](http://uecss.com/idea-settings.html "IDEA编辑器常用设置")

# 后记

今年六月份硬盘坏了，Eclipse丢了。想到要重装一堆Eclipse插件，配置成之前的Eclipse的状态，这个和重装OS一样繁琐，有转IntelliJ IDEA想法，再加上旁边几位IntelliJ IDEA粉丝积极推动，就用了。

IntelliJ IDEA安装后自带的插件已经很多：

1. 各种SCM支持：Git、SVN、Hg……
1. Maven支持
1. Spring支持
1. Web/JEE开发支持： JS、CSS、WAR、WebService、EJB、模板……
1. UT支持：JUnit、TestNG
1. UT测试覆盖率支持
1. Properties文件编辑支持
1. 用文件浏览器打开文件的所在的目录

常用功能的插件不需要自己装。