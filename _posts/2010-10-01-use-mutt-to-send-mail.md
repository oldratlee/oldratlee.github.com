---
layout: post
title: 使用mutt命令发送邮件
location: Hangzhou
permalink: /204/tech/shell/use-mutt-to-send-mail.html
write-time: 2010-10-01 14:06
tags:
- Shell
- mutt
- mail
---

Linux发送邮件命令，常常想到是的mail。但是这个命令配置比较麻烦。我在网上查了半天，还是没能使用成功。

但是发现另外一个发邮件的命令mutt（The Mutt E-Mail Client <http://www.mutt.org/>）

mutt的配置和使用都很简单。

交互式的使用方便也很方便，如下图。

![Mutt](/files/use-mutt-to-send-mail.jpg)

不读任何手册，照着界面上的提示就可以各种操作（如添加附件等）

一、安装Mutt
================

在Ubuntu下安装：

{% highlight bash linenos %}
sudo apt-get install mutt
sudo apt-get install msmtp
{% endhighlight %}

其它系统应该到网络上查一下。

二、配置Mutt
======================

这里只作最简单的配置，发邮件就好了。 详细的说明到官方网站 <http://www.mutt.org/>

a) 在配置文件 /etc/Muttrc 或是 ~/.muttrc 中添加

{% highlight bash linenos %}
set sendmail="/usr/bin/msmtp"
set use_from=yes
set envelope_from=yes
set realname="Jerry Lee"
set from=your_mail_id@163.com
{% endhighlight %}

/etc/Muttrc 或是 ~/.muttrc 两个文件只要在文件添加就可以了。

如果你没有root权限/etc/Muttrc文件是不能改的，这样自己的用户目录下创建文件~/.muttrc，添加上面的内容就好了。

b) 在配置文件 ~/.msmtprc 添加

{% highlight bash linenos %}
account default
host smtp.163.com
auth plain
user your_mail_id
from your_mail_id@163.com
password your_mail_password
{% endhighlight %}

需要修改.msmtprc文件的权限，否则Mutt会报错。

{% highlight bash linenos %}
$ chmod 600 .msmtprc
{% endhighlight %}

PS：   
用下面的命令可以检验SMTP服务器是否支持认证的TLS加密

{% highlight bash linenos %}
$ msmtp --host=smtp.126.com –serverinfo
SMTP server at smtp.126.com (m50-111.126.com [123.125.50.111]), port 25:    126.com Anti-spam GT for Coremail System (126com[20101010])Capabilities:    PIPELINING:        Support for command grouping for faster transmission    STARTTLS:        Support for TLS encryption via the STARTTLS command    AUTH:        Supported authentication methods:        PLAIN LOGIN This server might advertise more or other capabilities when TLS is active.
{% endhighlight %}

三、测试Mutt
====================

使用下面的命令测试一下

{% highlight bash linenos %}
$ echo hello world | mutt -s "test mail" -a ~/.bash_history –- receiver_mail_id@gmail.com
{% endhighlight %}

到你发的邮件去看看，是不是有一封测试邮件了。

参考资料
======================

- 如何在Ubuntu下使用命令发送邮件？ <http://hi.baidu.com/2young22/blog/item/0b2f311e5676af06314e1528.html>
- MuttFaq/Header <http://wiki.mutt.org/?MuttFaq/Header>
- The Mutt E-Mail Client <http://www.mutt.org/>
