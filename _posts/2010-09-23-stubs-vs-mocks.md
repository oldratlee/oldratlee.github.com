---
layout: post
title: Stubs和Mocks区别 (Stubs vs. Mocks)
location: Hangzhou
permalink: /76/tech/java/stubs-vs-mocks.html
write-time: 2010-09-23 0:57
tags:
- Martin Fowler
- unit test
- stubs
- Groovy
- mocks
- Java
---

翻译自《Programming Groovy － Dynamic Productivity for the jdk Developer》的P243。

原文如下：

Stubs vs. Mocks
==================

In the article “Mocks Aren’t Stubs,” (http://martinfowler.com/articles/mocksArentStubs.html ), Martin Fowler discusses the difference between stubs and mocks. A stub stands in for a real object. It simply reciprocates the coached expected response when called by the code being tested. The response is set up to satisfy the needs for the test to pass. A mock object does a lot more than a stub. It helps you ensure your code is interacting with its dependencies, the collaborators, as expected. It can keep track of the sequence and number of calls your code makes on the collaborator it stands in for. It ensures proper parameters are passed in to method calls. While stubs verify state, mocks verify behavior. When you use a mock in your test, it veriﬁes not only the state but also the behavior of the interaction of your code with its dependencies. Groovy provides support for creating both stubs and mocks, as you will see in Section 16.10, Mocking Using the Groovy Mock Library, on page 254.


翻译如下：

Stubs和Mocks的区别
======================

在文章“Mocks不是Stubs”中(<http://martinfowler.com/articles/mocksArentStubs.html>)，马丁·福勒讨论了stubs和mocks之间的区别。

stub代表的是一个真实的对象。在测试代码中被调用时，它简单的按照之前对它设定来应答调用者（对stub应答的设定过程称这为训练coach）。对stub应答的设定是为了通过测试。

mock对象要比stub做的事情多得多：

- 帮你确定你的代码和它的依赖（称为合作者collaborator）有你期望的交互。
- 跟踪你的在Mock对象代表的合作者执行调用的序列和次数。
- 保证方法调用传递了合适的参数。

stubs只检查的是状态（state），而mocks检查了行为（behavior）。在测试中使用mock，不仅检查你测试和其依赖之间和状态，而且还有行为。

PS
=============

顺便推荐一下《Programming Groovy － Dynamic Productivity for the jdk Developer》这本书。

这是一本Groovy进阶的书，入门可以先看一下《Groovy Recipes －Greasing the Wheels of jdk 》或是《Groovy Programming －An Introduction for jdk Developers》。Groovy的学习资料的介绍可以参见：<http://oldratlee.com/87/groovy-study-info.html>。

《Programming Groovy》中有关的元编程的讲解应该是最全面的。

更好的是，对动态语言的编程的最佳实践也讲了很多。

(整理过来的，2010年03月09日发在 <http://blog.csdn.net/oldrat/archive/2010/03/09/5359198.aspx>)