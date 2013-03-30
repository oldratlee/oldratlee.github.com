---
layout: post
title: 【转】你应该更新的Java知识之常用程序库
location: Hangzhou
permalink: /post/2013-02-02/new-popular-java-libraries
write-time: 2013-01-18 15:35
tags:
- java
- 常用程序库
- 常用
- 程序库
- library
- 更新
---

这些库有的解决了平时常见又重要的问题，有的则让Coding变得更惬意，值得全速跟进了解！

原文
===============

在很多人眼中，Java已经是一门垂垂老矣的语言，但并不妨碍Java世界依然在前进。如果你曾离开Java，云游于其它世界，或是每日只在遗留代码中挣扎，或许是时候抬起头，看看老Java中的新东西。

Guava
======================

一句话，只要你做Java项目，就应该用Guava。

guava是Google出品的一套Java核心库，在我看来，它甚至应该是JDK的一部分。作为一个Java程序员，如果你没抱怨过JDK的设计，只能说明一点，你写得程序还是太少。正是JDK设计不彰，才有了一些项目来补充JDK的不足。如果说老Java程序员应该听说过Apache Commons Lang，那新Java程序员该知道的就是Guava了。

老Java程序员更多的是知道Google Collections，不妨到它的主页上走一遭，你会看到这个库已经改名为Guava。事实上，Guava并不直接等于Google Collections，Guava是一个超集。Guava实在太强大了，要想展现它的强大，需要专门的介绍，这里就不展开了。

下面以一个统计单词出现个数的小程序作为这个段落的结尾，虽然无法与许多其它语言的实现相提并论，但作为一个Java程序员，你不妨想一下按照传统方式，这段代码应该是什么样子。

{% highlight java %}
String content = Files.toString(new File(args[0]), Charset.defaultCharset());
Iterable texts = Splitter.on(CharMatcher.WHITESPACE)
                                                 .omitEmptyStrings()
                                                 .trimResults()
                                                 .split(content);
Multiset collection = HashMultiset.create(texts);
{% endhighlight %}

Joda Time
======================

你觉得一个API设计得差到什么份上，才会把自己差不多的API全部Deprecated掉。java.util.Date便是这样的奇葩。因为它的API几乎都是反直觉的，几乎所有敢于用它的Java程序员都吃过它的亏。想初始化个2013年的第一天，还真不那么容易：

{% highlight java %}
Date firstDayOf2013 = new Date(113, 0, 1);
{% endhighlight %}

如果你是个Java新手，你能猜出113是从哪来的吗？（好吧，它是2013-1900，至于为什么是1900，这真得问API的设计者了）。

Joda Time就是人们实在无法忍受这样东西的产物。同样的代码用Joda Time实现：

{% highlight java %}
DateTime firstDayOf2013 = new DateTime().withDate(2013, 1, 1);
{% endhighlight %}

无论如何，你知道这能看出这些参数的含义了。不只如此，你还可以计算两天后是什么日子：

{% highlight java %}
firstDate.plusDays(2);
{% endhighlight %}

日期格式化，也是JDK Date系列API里一大特色，你必须把代码写成下面这样：

{% highlight java %}
new SimpleDateFormat("yyyy.MM.dd").format(firstDayOf2013)
{% endhighlight %}

作为一个初始化很慢的构造函数，你还必须每次调用，因为它不是线程安全的。同样的代码，在Joda Time里，我们可以用DateTimeFormatter：

{% highlight java %}
DateTimeFormatter formatter = DateTimeFormat.forPattern("yyyy.MM.dd");
...
formatter.print(dateTime);
{% endhighlight %}

请尽管放心大胆把formatter声明成一个字段，因为它是线程安全的。

当然，Joda Time的强大远不止于此。当然，JDK也并不是那么完全的自暴自弃，于是，有了一个JSR 310专门设计新的Date API。JSR 310的spec lead是Steven Colebourne，此人正是Joda Time的作者。不过，虽然JSR 310为我们描绘了Date的全新景象，但Java 8出来之前就先别打它的主意了，乖乖地用Joda Time吧。


Hamcrest
=====================

一句话，如果你写单元测试，就应该用Hamcrest。

如今不写单元测试，你都不好意思说自己在做工程项目了。但你一般这么写断言呢？如果你还写成下面这样，我只能说你落伍了：

{% highlight java %}
assertEquals(a, b);
{% endhighlight %}

请告诉我，哪个是执行结果，哪个是预期结果，不管你是怎样，反正大多数情况下，我是记不住的。所以，这个在只重功能不重可读性年代产生的API该更新了。于是，Hamcrest就是为了解决这样的问题而生的。

{% highlight java %}
assertThat(a, is(b));
{% endhighlight %}

很明显，前面一个是执行结果，后面一个是预期结果，当然这只是一个简单的例子。由于Hamcrest引入了matcher的概念（就是你看到的is部分），我们可以进行更多的组合：

{% highlight java %}
assertThat(number, greaterThan(5));
assertThat(text, startsWith("Hello"));
assertThat(array, hasItem("World"));
{% endhighlight %}

Hamcrest如此好用，以至于JUnit已经将其吸纳进去。如果你现在用的JUnit是4.4之后的版本，那你已经有了Hamcrest。无需额外的配置，就可以拿过来用。

Mockito
=======================

写单元测试不用Mock框架几乎是一件不可能的事，我是说Mock框架，不是Mock模式哦！对于老Java程序员来说，提起Mock框架，率先在脑海中撞线的多半是JMock或EasyMock。

使用Mockito，只要有一个理由就够了，简单。相比于JMock，它不用写checking，相比于EasyMock，它省去了replay。下面是个例子：

{% highlight java %}
List mockedList = mock(List.class);
when(mockedList.get(0)).thenReturn("first");
System.out.println(mockedList.get(0));
{% endhighlight %}

当然，Mockito还是非常强大的。

最后再强调一遍，无论使用哪个框架，请尽量不要使用verify，也就是传说中的Mock模式，那是把代码拉入泥潭的开始。

SLF4J和Logback
==========================

日志几乎是稍微有点规模的项目躲不开的一个东西，如果你是个老Java程序员，你必然知道Log4J，多半也知道Commons Logging。是时候把它们扔掉了，因为有SLF4J和Logback了。SLF4J要替代Commons Logging，而Logback的目标是Log4J。

程序员里愤青多，SLF4J和Logback的作者就是一个，他叫Ceki Gülcü，事实上，他也是Log4J的作者。Log4J的开发状态实在让他太不爽了，于是，他另起炉灶，打造出新的替代品。

只凭一点就足以让我们对SLF4J义无反顾了，你还记得用Commons Logging写出这样的代码吗？

{% highlight java %}
if (logger.debugEnable()) {
  logger.debug("Hello, ", name);
}
{% endhighlight %}

而SLF4J的写法只有一句话：

{% highlight java %}
logger.debug("Hello, {}", name);
{% endhighlight %}

从根源来说，这是时代造成的，Commons Logging是Java 5之前产生的，那时候还没有变参，所以，我们不得不说，它老了。

至于Logback，性能是最重要的噱头，当然，还有一些其它的理由。理由里有一点并未提及，但对于开发人员很贴心的改进，就是日志模式的改进，还记得Log4J那密码一样的日志模式吗？

{% highlight java %}
%d{dd MMM yyyy HH:mm:ss} [%t] %-5p %m%n
{% endhighlight %}

下面是Logback的版本，不用查文档，我也看出每段表示的都是什么：

{% highlight java %}
%d{dd MMM yyyy HH:mm:ss} [%thread] %-5level %msg%n
{% endhighlight %}

这里介绍的几个程序库都是很通用的，无论是你做怎样的开发，应该都或多或少给你一些帮助。时间未曾停步，Java开发也未曾停留，如果你是个老Java程序员，是时候更新一下自己的知识了。

原文链接
================

- [你应该更新的Java知识之常用程序库（一）](http://dreamhead.blogbus.com/logs/226738702.html "你应该更新的Java知识之常用程序库（一）")
- [你应该更新的Java知识之常用程序库（二）](http://dreamhead.blogbus.com/logs/226738756.html "你应该更新的Java知识之常用程序库（二）")