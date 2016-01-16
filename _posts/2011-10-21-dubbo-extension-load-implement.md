---
layout: post
title: Dubbo的扩展点加载（ExtensionLoader）的实现方式
location: Hangzhou
permalink: /430/tech/java/dubbo-extension-load-implement.html
write-time: 2011-10-21 16:52
tags:
- extension
- dubbo
- Service
- extensionloader
- classloader
- class
- servicelocator
- Java
---

Java有几个常用扩展点加载的实现：

- 标准的Java Service（sun.misc.Service/java.util.ServiceLoader）
- Spring classpath*
- OSGi

都可以做到新加入一个Extension的Jar在启动时甚至是运行时发现新的扩展点。

Dubbo的扩展点实现方式采用了标准Java Service，使用相同的配置文件，在此之上结合Dubbo的使用方式作一些加强。下面给出说明。

一、Java的ServiceLoader
============================

标准的Java的Service的说明在这里有说明： 
<http://download.oracle.com/javase/1.5.0/docs/guide/jar/jar.html#Service%20Provider>

JDK5类在对应的是 sun.misc.Service ，到了JDK6后移到了 java.util.ServiceLoader ，成为了标准Java API了。源代码 <http://www.docjar.com/html/api/java/util/ServiceLoader.java.html>

{% highlight java %}
// 常量及设置好ServiceLocator属性
final String PREFIX = "META-INF/services/";
Class service = ...; // Service接口
ClassLoader classloader = ...; // 所用的ClassLoader
 
String fullName = PREFIX + service.getName();
Enumeration configs = classloader.getResources(fullName);
Set<String> providerClassNames = new HashSet();
while (configs.hasMoreElements()) {
     URL u = configs.nextElement();
 
     ArrayList names = new ArrayList();
     InputStream in = u.openStream();
     BufferedReader r = new BufferedReader(new InputStreamReader(in, "utf-8"));
     ... // 从取每一行（Service Provider的类名）放入 Set providerClassNames 
}
 
// 拿到providerClassNames后，通过 Class.forName(classname, true, loader).newInstance()方法，
// 加载类Provider类，并返回Service的Provider类的各个Instance
{% endhighlight %}

【未完待续ing…】