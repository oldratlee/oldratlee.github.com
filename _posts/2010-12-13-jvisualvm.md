---
layout: post
title: VisualVM——JDK自带的性能分析工具
location: Hangzhou
permalink: /352/tech/java/jvisualvm.html
write-time: 2010-12-13 21:28
tags:
- JLS
- 生活
- blog
- Java,
---

引子
====================

这段时间项目新版本要发了，所以跟着QA分析性能测试和压力测试，用了平时不怎么用的很多工具：

- jmap  
jmap -heap pid → 查看堆的使用状况信息   
jmap -histo:live pid | less → 堆中活动的对象以及大小   
jmap -dump:format=b,file=eclipse_heap.bin pid → Dump堆信息  
- jstat，可以查看很多内容
jstat -gcutil  -h 20 pid  1000 100 → 查看Java进程GC的情况，1000ms统计一次gc情况统计100次  
jstack，查看jvm线程运行状态，是否有死锁现象等等信息   
jstack pid → Dump线程信息  
- jinfo，查看jvm配置信息
- jps
- jconsole，图形界面。可以持续收集内存、线程信息，并以曲线的方式显示出来。   
# 这个工具真正要做的功能是查看JMX Bean的信息，性能分析中并不关心JMX信息。  
- mat :idea: ，分析内存Dump，查找内存泄漏。http://www.eclipse.org/mat/  
- JProfiler，商业 Profile工具。查看内存、CPU的使用，强劲
 

VisualVM在JDK1.6 Update7之后的版本中推出，就放在bin目录下面。

VisualVM在的官方网站在 https://visualvm.dev.java.net 。JDK1.6 Update7之前的版本可以单独下载使用。

VisualVM使用简单，几乎0配置，功能还是比较丰富的，几乎囊括了其它JDK自带命令的所有功能。

- 内存信息
- 线程信息
- Dump堆（本地进程）
- Dump线程（本地进程）
- 打开堆Dump。堆Dump可以用jmap来生成。
- 打开线程Dump
- 生成应用快照（包含内存信息、线程信息等等）
- 性能分析。 :idea: CPU分析（各个方法调用时间），内存分析（各类对象占用的内存）
- ……


PS:

跟着QA做性能测试和压力测试，很多收获和感触。

- 虽然有UI工具，尤其是商业的工具如JProfiler，功能异常强大，但是命令行工具还是非常有市场的。
	- 线上出问题时，命令行能方便和快速的Dump出Heap信息和线程信息，然后再对Dump分析；或是JVM信息，如GC次数、JVM参数。命令行对目标的产生的压力小，UI工具可能无法连上。
	- JDK自带命令行是环境标配的，你总是可以使用。UI可能要安装，等装好，菜都凉了。
- 往往是对工具的不了解才不能解决问题，JDK自带的工具已经可以解决很多问题了。强大的商业工具往往只是增加学习难度。
- 只有测试得出的结果才是可信的，之前的推测往往不可靠的。
- 系统地收集和保证数据，这样当发现问题时，就有更可以用作判断的信息。
 

参考资料
====================

- 好用的性能分析工具–VisualVM
- 可与jprofiler/yourkit媲美的java诊断工具Visualvm
- visualvm的官方网站： https://visualvm.dev.java.net/download.html
一个Eclipse插件，启动时附带启动visualvm来作性能分析：https://visualvm.dev.java.net/eclipse-launcher.html