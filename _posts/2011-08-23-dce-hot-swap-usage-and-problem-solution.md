---
layout: post
title: DCE使用的问题及其解决方法
location: Hangzhou
permalink: /383/tech/java/dce-hot-swap-usage-and-problem-solution.html
write-time: 2011-08-23 21:01
tags:
- class
- debug
- dce
- Java
- hot swap
---


转于自己在公司的Blog：<http://pt.alibaba-inc.com/wp/experience_1368/dce-hot-swap-usage-and-problem-solution.html>

目前，国际站目前还是主要在几个应用上，一个应用多的有三四十万行代码。几乎所有的产品线在这个应用上都有代码；采用分支开发，要改的代码可能只有一点也要Check out出整个工程的代码来。

这样大工程，对于开发效率的影响很大，编译一下10分钟，启动一下5分钟。苦闷的等待是时间的浪费，另一方面也是打断了开发的节奏。开发过程中，每修改了一点内容，就要编译工程、重启应用来验证。每个开发员都会要频繁重启，浪费总量上是巨大的。

当然解决大应用的关键是拆根据功能拆分成小应用，这件事国际站也在积极进行中。

Hot Swap可以在Debug时让对源文件的修改立即生效，减少编译和重启的次数，节省开发时间浪费。

Java虚拟机的缺省的Hot Swap机制只允许修改类的方法体，这个限制太大。

DCE(*the Dynamic Code Evolution VM*)是一个允许在运行状态下无限制的修改加载类文件的Java虚拟机补丁，即Hot Swap的加强。使用DCE以后，可以

- 增加、删除 类的属性、方法
- 改变一个类的父类

这样的加强，完全满足平时开发时需要。DCE是基于GPL v2.0开源协议的。你可以通过Windows，Linux，Mac OS下载DCE的源代码或者可执行文件。

下面记录了DCE使用中遇到的和整理了网上提到的对DCE的问题及其解决。

DCE注意
================

Linux下，DCE目前只支持32位JVM，不支持64位JVM。

与JDK 1.6 update 26有兼容问题，使用JDK 1.6 update 25。  
\# 参见官网<http://ssw.jku.at/dcevm/binaries/>的说明。

问题及其解决方法
=====================

下面是在国际站开发环境集成DCE的过程中，收集的问题以及解决方法的记录总结和汇总。

1. 和asm、cglib相关的jar包冲突
------------------------------------

异常：

{% highlight java %}
Caused by: java.lang.NoSuchMethodError: org.objectweb.asm.ClassWriter.<init>(Z)V
    at net.sf.cglib.core.DebuggingClassWriter.<init>(DebuggingClassWriter.java:47)
    at net.sf.cglib.core.DefaultGeneratorStrategy.getClassWriter(DefaultGeneratorStrategy.java:30)
    at net.sf.cglib.core.DefaultGeneratorStrategy.generate(DefaultGeneratorStrategy.java:24)
    at net.sf.cglib.core.AbstractClassGenerator.create(AbstractClassGenerator.java:216)
    at net.sf.cglib.core.KeyFactory$Generator.create(KeyFactory.java:145)
    at net.sf.cglib.core.KeyFactory.create(KeyFactory.java:117)
    at net.sf.cglib.core.KeyFactory.create(KeyFactory.java:108)
    at net.sf.cglib.core.KeyFactory.create(KeyFactory.java:104)
    at net.sf.cglib.proxy.Enhancer.<clinit>(Enhancer.java:69)
    at org.hibernate.proxy.pojo.cglib.CGLIBLazyInitializer.getProxyFactory(CGLIBLazyInitializer.java:117)
    at org.hibernate.proxy.pojo.cglib.CGLIBProxyFactory.postInstantiate(CGLIBProxyFactory.java:43)
{% endhighlight %}

参见DCE的JIRA<http://kenai.com/jira/browse/DCEVM-4>

原因：dcevm.jar文件中包含了一份ASM类，版本较老，并优先加载。（阿干发现这个问题，并给出重命名包名的解决方法）

### 解决方法1

重命名dcevm.jar文件中asm的包名，从 org.objectweb.asm 重命名成 dce.org.objectweb.asm。
\# 在Jar文件上重命名包名 使用工具： JarJar（http://code.google.com/p/jarjar/）。

### 解决方法2

asm、cglib换成了新的版本：asm-3.3.1.jar、cglib-nodep-2.2.jar。   
asm、cglib各版本匹配注意点:   
asm 1.5.3.jar 匹配 cglib-2.1.3.jar    
asm-2.X.jar asm-3.x.jar 匹配  cglib-nodep-2.1_3.jar

2. 动态添加的static属性，例如 private static String attrib1 = "x"，调用时会显示attrib1是null。
-----------------------

3. 在一个正在执行的循环中，改变可能不能生效。例如：
------------------

{% highlight java %}
public static void main(String[] args) {
       for (int i = 0; i < 10000; i++) {
           test(); //sleep 1s and print something
       }
}
{% endhighlight %}

修改为:

{% highlight java %}
public static void main(String[] args) {
        for (int i = 0; i < 10000; i++){
            test();
            System.out.println("xxx");
        }
}
{% endhighlight %}

xxx是不能输出的。

但test方法体内部的修改是可以生效的。例如：

{% highlight java %}
public static void main(String[] args) {
        System.out.println("xxx");
        for (int i = 0; i < 10000; i++) {
            test();
        }
}
{% endhighlight %}

5. Crash when running maven test goal with jmockit
---------------

参见DCE的JIRA http://kenai.com/jira/browse/DCEVM-3

6. DCEVM启动报错
--------------

{% highlight java %}
Must use the serial GC in the Dynamic Code Evolution VM
Could not create the Java virtual machine.
{% endhighlight %}

把JAVA启动参数中并发GC的选项删除，如：

{% highlight java %}
-XX:+UseConcMarkSweepGC
-XX:+CMSIncrementalMode
-XX:+CMSIncrementalPacing
-XX:CMSIncrementalDutyCycleMin=0
-XX:CMSIncrementalDutyCycle=10
开发模式下，修改这些选项不会有功能上的影响。
{% endhighlight %}

一些参考资料
=================

- DCE官方网址 <http://ssw.jku.at/dcevm/>
- hotswap 用户手册 - 淘宝JAVA中间件团队博客 <http://rdc.taobao.com/team/jm/archives/641>
- hostswap dcevm - 使用介绍 <http://www.cnblogs.com/redcreen/archive/2011/06/03/2071169.html>
- Dynamic Code Evolution for Java dcevm 原理 <http://www.cnblogs.com/redcreen/archive/2011/06/14/2080718.html>
- Java HotSpot dcevm 在debug模式下的热部署 <http://sjsky.iteye.com/blog/907606>
- 深入 Java 调试体系 <http://www.ibm.com/developerworks/cn/java/j-lo-jpda1/>
- Java Platform Debugger Architecture <http://java.sun.com/javase/technologies/core/toolsapis/jpda/>
