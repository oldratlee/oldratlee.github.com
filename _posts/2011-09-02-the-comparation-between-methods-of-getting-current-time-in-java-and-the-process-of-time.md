---
layout: post
title: Java获取当前时间方法的比较以及时间的处理
location: Hangzhou
permalink: /410/tech/java/the-comparation-between-methods-of-getting-current-time-in-java-and-the-process-of-time.html
write-time: 2011-09-02 13:10
tags:
- XX
- YY
---

一、获取当前时间
==========================

在Java中获取当前时间我一般总是使用long java.lang.System.currentTimeMillis()方法，Long类型是基本类型，运算（如比较时间的前后）、传输和存储都很方便。

当然，如果有的方法要使用java.util.Date类型，可能通过构造函数new Date(long date)来切换类型。java.util.Date.getTime()方法，则把Date类型切换成Long类型。当然使用Date的缺省构造函数new Date()来获得当前的时间的Date。

查看国际站的代码你会看到不少代码使用了java.util.Calendar.getInstance()方法。再通过java.util.Calendar.getTime()来获取Date，或是通过java.util.Calendar.getTimeInMillis()来获取Long。

或是Google一下“Java获取当前时间”，会看到很多人也是推荐使用Calendar，说Date很多方法已经@Deprecated。

我个人不赞同这一点，原因如下：

- 简单方法能解决问题。Calendar显然概念上复杂很多，另两种用法用意也是非常的清晰。
- 性能问题。看下面运行数据。 
（不要一开始就考虑性能问题，性能问题往往不会成为问题；性能问题往往也是由上一点复杂性引起的）
三个方法运行10M次所用时间：（在我开发机上的运行时间，只是示意一下比例）

```java
System.currentTimeMillis() 10M times: 169ms
new Date() 10M times: 283ms
Calendar.getInstance() 10M times: 6625ms
# java.util.Calendar.getInstance()方法消耗时间 约是 new Date() 的 20倍。
```

二、时间的处理
==========================

上面说了获取当前时间不要用Calendar类，那什么时候使用Calendar类呢？

Long类型，表示了时间的值；Date类型，提供了几个获取年月日的方法、字符串，还有对象的标准方法()。

时间的操作：

- 比较时间前后、计算两个时间差值。  
- 计算出时间对应的的年月日、所在的周数，等等。   
这些计算涉及其它的信息：所在时区（不同时区几点是不一样的），本地化信息（输出成中文的星期一、还是英文的Monday）    
做这些计算正是实现复杂的Calendar类要解决的问题。
- 常用的时间日期打印操作，比如“2012年3月4日 15:16:17”，显然有年月日的计算，涉及了时区、本地化。
- 记录、存储、传输时间，使用long类型（只需4字节存储空间）、Date类型。时间计算、显示时间使用Calendar类提供的方便方法。

