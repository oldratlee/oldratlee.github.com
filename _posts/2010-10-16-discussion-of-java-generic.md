---
layout: post
title: Java泛型相关的常见问题小汇
location: Hangzhou
permalink: /268/tech/java/discussion-of-java-generic.html
write-time: 2010-10-16 14:59
tags:
- template
- generic
- Java
---

Java5加入了泛型，有了泛型可以避免丑陋的类型转换操作，也大大提高了代码的可读性。既然如此，可以声明类型参数时，我们尽量要使用泛型。

这里只是列出常见的泛型使用问题，给出精简的解释和快速的解决方法，只是一个抛砖引玉。

更全面分析和阐述的资料在我的文章[《Java泛型学习资料小汇》](http://oldratlee.github.io/278/study-material-of-java-generic.html)中有整理，比我说的好得太多 :)

希望你看了这里的内容，能引发你继续去读上面列的资料的冲动。

![Cup<Tea>](/files/java-cpu-tea.jpeg)

来上一 杯<茶> 吧，看看泛型相关的问题 :idea:

1. 无法创建泛型的数据
===========================

{% highlight java %}
List<String>[] list1 = new ArrayList<String>[3]; // 编译错
List<String>[] list2 = new ArrayList[3]; // 编译警告
{% endhighlight %}

第一行的错误信息是 “Cannot create a generic array of ArrayList<String>”。

《Effective Java》的第二版的第25条给出创建泛型数组是非法的原因说明。大家一定要去看看 :)

简单的说，这样做不是类型安全的。

解决方法是：

避免使用泛型数据。可以使用List<List<String>>

创建时，去掉泛型参数，即是第二行的方式。这会有一个“unchecked”的编译警告。

2. 下面的代码涉及了简单的泛型类型参数类型范围。你能看懂和使用吗
=======================================

{% highlight java %}
public static void foo(List<? extends Number> list) {
    list.add(new Integer(2)); // 编译通过么?  Why ?
}
public static void bar(List<? super Number> list) {
    list.add(new Integer(2)); // 编译通过么?  Why ?
    list.add(new Float(2)); // ok?
}
{% endhighlight %}

3. Java5中Enum的源代码中，它是这么定义的：
===========================

{% highlight java %}
public abstract class Enum<E extends Enum<E>>
    implements Comparable<E>, Serializable {
{% endhighlight %}

这个类似递归结构的Enum<E extends Enum<E>>表达了什么含义？

4. 在Collections工具类中的max方法声明的含义？
===================

{% highlight java %}
public static <T extends Object & Comparable<? super T>>T max(
    Collection<? extends T> coll) {
{% endhighlight %}

这个返回类型的声明更复杂了，表达了什么含义？

5. 对于类型声明<?>与<? extends Object>是否等同？
========================


PS:  
未完待继，NND，真是不好写。完持续补充。

本来的打算的写的东西挺多的，但是自己查资料越多，发现要写的东西越少，别人已经写了很多，写得更好。与其我来写，还不如给出资料的汇总。

结果删来删去，只写了常见的问题，以及快速的解决方法。
