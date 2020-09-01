---
layout: post
title: 发布及其检查的自动化实践
location: Hangzhou
permalink: /post/2013-01-22/deploy-check-automation
write-time: 2013-01-22 10:38
tags:
- 发布
- 检查
- 验证
- 自动化
- 实践
- Dubbbo
- 注册中心
- 服务
- 监控
---

转自发在公司的博文： [发布及其检查的自动化实践](http://rdc.taobao.com/team/jm/archives/2753 "发布及其检查的自动化实践")

这里记录的是Dubbo注册中心的发布过程中的自动化改进点。实践是通用的，希望可以能给你一些借鉴和启发。

Dubbo注册中心记录整个网站服务信息，服务消费者（Consumer）通过注册中心获得服务提供者（Provider）列表，才能完成服务调用。注册中心是网站服务的一个关键组件。  
\# 现在一个站点的注册中心上的服务Consumer和Provider就有35K+。

随着注册中心的服务越来越多，注册中心上的功能越加越多，注册中心上线发布和验证的过程变得越来越复杂繁琐，发布过程常常会出些问题，发布过程变得危险重重。

应付这些复杂就是持续的改进发布及其检查。

(1) 数据库配置错误
==================

阿里系线上发布有，**生产环境**和**预发布环境** 2套，这2套环境是隔离的，不同环境的注册中心使用不同的DB。应用先在预发布环境发布，检验没有问题后在生产环境上发布。

发布是由SCM来做的，通过使用不同配置文件（包含不同的DB配置）打包发布。

人操作就会有出错的可能。有一次SCM在生产环境配置了预发布环境的DB。DB中有预发布环境的Provider数据，导致线上应用出错。

解决方法
----------------------

监控注册中心布置目录下数据库配置文件值是否正常。

Dubbo注册中心的DB信息配置在`jdbc.properties`文件中：

```bash
database.url=jdbc:mysql://1.2.3.4/dubbo_registry
database.username=dubbo_registry
database.password=dubbo_registry
......
```

监控这个配置中database ip、用户名的值是否预期的。

PS：监控 文件的内容，URL的输出结果，URL响应时间，硬盘、内存、网络使用量 等等是监控系统的基本功能。

原则
-----------------------

> 人的操作是可能出错的操作。  
可能出错的地方都加上监控，这样出错了就会报警出来。  
这样的监控项应该一直开着。

(2) Consumer和Provider的数据核对
==========================

注册中心的发布后，要Consumer和Provider核对发布前后是否有缺失。

- 如果没有缺失，安心完成发布。
- 如果有缺失，则要处理，找出原因，比如，少的Provider是真的下线了？注册中心原来的脏数据？自己不能处理，则要联系对应应用的负责人一起确认处理。

解决方法
----------------------

1) 注册中心提供URL可以Dump出Provider、Consumer的数据：（实际上也会做方便查看的页面。）  
\# 提供URL是可以方便和脚本集成，可以用其它的方式。

```bash
13210 provider instance

dubbo://10.200.216.119:20882/com.aliyun.crm.panorama.webx.BucAclService com.aliyun.crm.panorama.webx.BucAclService:1.0.0
dubbo://10.246.130.110:20880/com.alibaba.havana.api.member.JoinService hz_idc/com.alibaba.havana.api.member.JoinService:1.0.0
dubbo://10.246.130.110:20880/com.alibaba.havana.api.member.MemberService com.alibaba.havana.api.member.MemberService:1.0.0
dubbo://10.246.130.110:20880/com.alibaba.havana.api.member.MemberService hz_idc/com.alibaba.havana.api.member.MemberService:1.0.0
dubbo://10.246.130.110:20880/com.alibaba.havana.api.member.OperatorService hz_idc/com.alibaba.havana.api.member.OperatorService:1.0.0
......
```

2) 在发布的关闭脚本 和 启动脚本中，添加逻辑：把数据Dump到文件中

```bash
wget http://127.0.0.1:${APP_PORT}/sysinfo/dump/providers \
    --timeout=15 --output-document=$DUMP_DIR/providers.dump \
    --output-file=$DUMP_DIR/providers.dump.log 
```

3） 添加一个脚本，Diff数据前后的Provider数据。比如简单的实现可以是

```bash
$ diff dir1/provider.dump dir2/provider.dump
```

直接使用diff命令输出的格式不方便直接阅读，可以实现更好一些。

4) 启动完成，注册中心稳定后，执行上面的Diff脚本，快速找到差异。

原则
-----------------------

> 对于繁琐的检验操作，可以在程序添加逻辑输出需要的数据。  
在发布脚本中Dump好需要核对的数据。  
再通过脚本来自动完成核对操作。

(3) 注册中心状态报告
===========================

- 为了加速，注册中心有Cache数据，比如 注册的Provider信息先在内存中，然后异步写入DB。如果这样的Cache如果长时间有数据，说明有问题。
- 执行任务的线程池状态。如果线程池Active很高说明有问题。
- ......

这些业务状态在发布过程也需要检查。

解决方法
----------------------

注册中心有一个URL可以访问，列出这些状态的情况：

```bash
OK

[Cache]
FailedRegitered=0

[ThreadPool]
Active=30
Max=200
Idle=200
......
```

第一行是一个汇总行。   
\# 后面对比脚本可以忽略这一项。

这样可以加上一个监控项，监控这个URL返回是不是`OK`。

在发布的时候，只要检查这一个地方就可以拿到所有要的信息。不用到各个地方一项一项的查看。

原则
-----------------------

> 需要核对的数据，提供逻辑可以方便的查看。    
如果核对是有必要持续，则配置到监控系统中。

(4) 注册中心重启引发大量动态数据删写
=====================================

Provider和Consumer是动态数据，也就是Provider、Consumer断开连接后数据会被清除。

这样功能没有问题，线上有个问题是，在注册中心发布时，注册中心重启，连接断开，注册中心启动后，Consumer、Provider连接上来，注册中心会要把Consumer、Provider的数据删除和写入，造成了数据动荡：

- 大量数据删除和写入，注册中心压力很大。   
线上注册中心多次因为这个问题导致DB被大量操作Hang住，注册中心无法完成启动，相当惊险！
- Consumer可能收到的Provider列表缺失的（数据不完整），会引发应用的问题。  
比如Consumer收到的Provider列表只有一个Provider，Consumer调用到一台Provider上，会压垮Provider，结果服务调用出现失败。

解决方法
----------------------

注册中心可以设置warm-up状态，发布完成后，等待注册中心稳定后，关闭warm-up状态。  
\# “注册中心稳定”的意思是，Provider已经重新连接上来，Provider数据不会再动荡。

在warm-up状态时：

- Consumer和Provider的动态数据不会删除（写入允许）。
- 注册中心不通知Consumer，Provider列表的变化不推送给Consumer。  
\# 在注册中心发布过程中，Provider的变化很少，旧一点数据不会有问题。

有了这个功能，如果不能与发布脚本集成，那么发布过程就多个人肉操作，要和发布脚本集成。

仍然可以考虑注册中心提供一个URL来开关warm-up状态。

```bash
curl http://127.0.0.1:${APP_PORT}/sys/config?warmup=true

curl http://127.0.0.1:${APP_PORT}/sys/config?warmup=false
```

这样在发布开始时，发布脚本中开启warm-up状态；完成发布后等待注册中心集群稳定后，关闭warm-up状态。

warm-up一个临时状态，长时间处于warm-up状态会有故障。所以加上监控项：如果注册中心是warm-up状态，报警。  
\# 这个问题引发过故障，当时warm-up状态设置还是人肉完成，结果发布完成后忘记改回来了。  
\# 这个问题也引出另一个方面的思考，在我的下面这篇博文中说明： [准备一个安全可靠的发布流程](http://oldratlee.github.io/post/2013-01-22/safe-and-reliable-deploy-flow "准备一个安全可靠的发布流程") 。

总结
===========================

1. 越是容易出错的步骤越是要多执行，越需要自动化是执行。因为执行多了就可以发现容易出错的步骤，进而去找出发现出错的原因去改进，稳定可靠的执行。
2. 自动化至关重要。当然刚开始为了安全是手执行的，手动执行几次后:
	- 步骤会固定下来
	- 会出错的地方心中有数了

从上面的发现问题到自动化解决，整个过程如下图：

![发布及其检查的自动化](http://m3.img.libdd.com/farm5/2013/0123/10/0BE32C6D607EC6BF5DE67C80720CF3DC848E01A7F4E82_500_431.jpg)

随着发现的问题被逐步自动化，在正常的情况下，脚本运行完成返回正常，就有信心不再需要
人肉介入了。即理想情况下，发布操作只要运行脚本的“one-line”，极简人肉介入。   
\# 非正常情况，如有Provider丢失，则免不了要人肉参与，和用户一起排查解决问题。
\# 命令行的“one-line” vs. GUI的“one-click”
