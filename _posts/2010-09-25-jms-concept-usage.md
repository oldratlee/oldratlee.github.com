---
layout: post
title: JMS的概念和使用
location: Hangzhou
permalink: /103/tech/java/jms-concept-usage.html
write-time: 2010-09-25 20:41
tags:
- active mq
- mom
- jms
- Java
- middleware
---

Part 1 概念
=====================

一、中间件 ＆ 消息中间件（Message-Oriented Middleware、MOM）
-------------------------------------------------------------

参见[An Introduction to the JMS](http://oldratlee.github.io/115/tech/java/introduction-to-jms.html)。
 

二、JMS占了MOM分布式系统中哪些部分
-------------------------------------------------------------

说明这个问题之前首先要说明，涉及MOM的应用涉及的对象。

1. MOM
1. 应用，消息客户端
1. 消息API

JMS包含了哪些内容呢？

先说JMS是什么：

JMS是一个定义了一套接口和相关的语义的规范，以便使用Java编写的应用访问任何JMS兼容的MOM产品所提供的服务。

> JMS is a specification that defines a set of interfaces and associated semantics, which allow applications written in Java to access the services of any JMS compliant Message MOM product.

这个说明有点长，可以拆成下面的说法：

1. JMS只是规范，并不是一个实际的MOM实现。
1. 因为是个规范，所以只定义的接口和他们的语义。
1. 这些接口用于和JMS兼容的MOM产品交互。幸运的是，在市场上有很多JMS兼容的MOM产品，包括IBM的MQSeries、Progress Software的SonicMQ、Fiorano的FioranoMQ等待。JMS兼容的MOM被称为**JMS提供者（JMS provider）**。

简单地说，JMS是一个从Java应用访问MOM设施的API。（***JMS is an API used to access the facilities of a MOM from a Java application.***）

三、JMS API的设计
-------------------------------------------------------------

＃还没有写。

Part 2 使用
======================

一、环境使用JMS环境搭建
-------------------------------------------------------------

要做JMS的应用，当然要一个MOM产品。这里选用ActiveMQ。

这个是Apache的一个开源项目。性能好，并且安装、配置和使用都很非常的简单、方便。

### 1. 下载ActiveMQ

ActiveMQ主页： <http://activemq.apache.org/>

下载地址：<http://activemq.apache.org/download.html>

下载5.2.0的版本，相应的maven库也有了，方便开发。

下载下来的是一个压缩包。注意Windows和Linux/Unix要下载不同的文件。

解压缩开来，即可使用了。包含文件如下图：

### 2. 开启ActiveMQ

执行bin目录下的activemq-admin脚本

```bash
./activemq-admin start
```

###　3.  关闭ActiveMQ

执行bin目录下的activemq-admin脚本

```bash
./activemq-admin stop
```

二、Quick Start Example
-------------------------------------------------------------

下面给出一个JMS的简单程序：

在运行程序前先开启ActiveMQ。

程序没有什么注释，代码在做什么，方法名本身说明得很清楚了。

如果对方法名不理解，可以参考JMS的API文档，讲得很好。<http://java.sun.com/javaee/5/docs/api/>，JMS在包javax.jms下。

发送消息的程序：

```java

public class SimpleSender {
	private static final Log logger = LogFactory.getLog(SimpleSender.class);
	public static void main(String[] args) throws Exception {
		ActiveMQConnectionFactory activeMQConnectionFactory = new ActiveMQConnectionFactory(
		"tcp://localhost:61616");
		Connection connection = activeMQConnectionFactory.createConnection();
		connection.start();
		Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
		Destination destination = session.createQueue("JMS.DEMO");
		MessageProducer publisher = session.createProducer(destination);
		publisher.setDeliveryMode(DeliveryMode.PERSISTENT);
		// 发送一条消息
		TextMessage message = session.createTextMessage("Test Message");
		publisher.send(message);
		logger.info("Send message: " + message);
		session.close();
		connection.close();
	}
}
```

接收消息的程序：

```java
public class SimpleReceiver {
	private static final Log logger = LogFactory.getLog(SimpleReceiver.class);
	public static void main(String[] args) throws Exception {
		ActiveMQConnectionFactory activeMQConnectionFactory = new ActiveMQConnectionFactory(
		"tcp://localhost:61616");
		Connection connection = activeMQConnectionFactory.createConnection();
		connection.start();
		Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
		Destination destination = session.createQueue("JMS.DEMO");
		MessageConsumer consumer = session.createConsumer(destination);
		// 接收一条消息
		Message message = consumer.receive(500);
		if (message != null)
			logger.info(message);
		else {
			logger.warn("fail to receive a message!");
		}
		session.close();
		connection.close();
	}
}
```

JMS可以参考的资料
===================================

- sun的JMS主页：<http://java.sun.com/products/jms/>。 
- JMS的规范：<http://java.sun.com/products/jms/docs.html>。
- Java Message Service Tutorial：<http://java.sun.com/products/jms/tutorial/index.html>。
- Java EE API文档：<http://java.sun.com/javaee/5/docs/api/>，JMS在包javax.jms下。看JMS的API是最高效学习方法。
- ActiveMQ的网站：<http://activemq.apache.org/>
- 《Practical JMS》 ：这本书讲得是JMS 1.0.2，现在JMS版本是1.1。所以这本书讲的JMS使用有些过时，概念上没有问题。
- 《Java Message Service》第二版：讲的是 JMS 1.1。

PS:  
整理过来的内容，2009年8月30日写的。做Napoli项目要用到JMS。
