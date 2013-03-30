---
layout: post
title: Linux下JDK API java.net.InetAddress.getLocalHost().getHostAddress()返回出错
location: Hangzhou
permalink: /post/2012-10-24/jdk-api-getLocalHost
write-time: 2012-10-24 21:30
tags:
- XX
- YY
---

支持的[Dubbo](http://code.alibabatech.com/wiki/display/dubbo "Dubbo")过程会有人反馈服务提供者IP获取不对 或是 出异常：

```java
java.net.UnknownHostException: HostNameXxx: HostNameXxx
	at java.net.InetAddress.getLocalHost(InetAddress.java:1360)
	......
```

Dubbo实际使用的API是java.net.InetAddress.getLocalHost()来获取本机IP。  
使用JDK版本是1.6。

排查一下这个API返回出错的原因，排查结果如下：

## /etc/hosts中有配置主机名条目

如果/etc/hosts中有配置主机名条目，则返回主机名条目中配置的IP地址，不管这个IP地址是否是机器的实际IP地址。

```bash
127.0.1.1       your_host_name # 假定主机名是your_host_name
# Ubuntu系统，缺省有主机名的条目，IP地址是127.0.1.1
# 127.0.1.1是个Loopback IP
```

API运行一下结果：

```java
scala> java.net.InetAddress.getLocalHost().getHostAddress()
res0: java.lang.String = 127.0.1.1
```

阿里线上用的是RedHat，/etc/hosts中都会配置主机名的条目，用的是正确的IP。所以线上没有获取IP不对的问题。

主机名条目配置的是不对的IP，API getLocalHost也会愉快的返回。如：

```java
1.2.3.4 your_host_name 
```

API运行一下结果：

```java
scala> java.net.InetAddress.getLocalHost().getHostAddress()
res0: java.lang.String = 1.2.3.4
```
在Ubuntu和RedHat上都会愉快的返回这个不对的IP。

## /etc/hosts中没有配置主机名条目

如果/etc/hosts中没有配置主机名条目，则会走DNS解析。

我的开发机的机器名在DNS上查不到，API调用出错：

```java
scala> java.net.InetAddress.getLocalHost().getHostAddress()
java.net.UnknownHostException: HostNameXxx: HostNameXxx
	at java.net.InetAddress.getLocalHost(InetAddress.java:1360)
	......
```

线上的机器名可以DNS查到，上面的调用可以返回正确的IP。
