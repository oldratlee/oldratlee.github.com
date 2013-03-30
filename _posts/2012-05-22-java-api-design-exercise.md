---
layout: post
title: 通用Java I/O API设计练习总结
location: Hangzhou
permalink: 493/tech/java/java-api-design-exercise.html
write-time: 2012-05-22 19:27
tags:
- XX
- YY
---

转自自己在公司发的Blog http://code.alibabatech.com/blog/architecture_1489/java-api-design-exercise.html

博文[A generic input/output API in Java](http://www.jroller.com/rickard/entry/a_generic_input_output_api)中给出IO API的实现Demo代码。  
\# 这篇博文的译文见我的博文：[Java的通用I/O API](http://oldratlee.com/474/tech/java/generic-io-api-in-java-and-api-design.html)。

API设计分析见我的博文：[通用Java I/O API设计练习总结](http://oldratlee.com/493/tech/java/java-api-design-exercise.html)。

在组内分享时的PPT：[[API设计实例分析|ApiDesignSampleStudy.pptx]]

关键接口的依赖关系图
------------------------------

[[interface-dependency.jpg|alt=依赖关系]]

关键接口的调用序列图
------------------------------

[[invocation-sequence.jpg|alt=调用序列]]

图中一些约定：

* 红色的方框：创建对象、清理资源 操作
* 蓝色的方法：核心接口上的方法 
* 红色的方法：IO的操作所在的方法 

注： [[UML图源文件|generic_io_uml.asta]]，使用[Astah](http://astah.net/download#community)绘制。

包的功能
------------------------------

- package com.oldratlee.io.core  
核心接口
- package com.oldratlee.io.core.filter  
实现的Filter功能的类
- package com.oldratlee.io.utils  
工具类
- package com.oldratlee.io.demo  
Demo示例的Main类

可以进一步学习API设计的资料
------------------------------

* How to Design a Good API and Why it Matters(by Joshua Bloch) 【[[本地下载|How-to-Design-a-Good-API-and-Why-it-Matters(by-Joshua-Bloch).pdf]]】  
<http://lcsd05.cs.tamu.edu/slides/keynote.pdf>
* The Little Manual of API Design 【[[本地下载|The-Little-Manual-of-API-Design.pdf]]】  
<http://chaos.troll.no/~shausman/api-design/api-design.pdf>
* Practical API Design: Confessions of a Java Framework Architect  
<http://www.amazon.com/Practical-API-Design-Confessions-Framework/dp/1430243171>  
这本书已经出了中文版：[《软件框架设计的艺术》](http://book.douban.com/subject/6003832/)
* Google Search  
<http://www.google.com.hk/search?&q=api+design>