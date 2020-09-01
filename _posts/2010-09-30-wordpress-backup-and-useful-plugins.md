---
layout: post
title: 备份你的Wordpress，推荐几个好用的插件
location: Hangzhou
permalink: /171/life/wordpress-backup-and-useful-plugins.html
write-time: 2010-09-30 17:27
tags:
- shell
- 生活
- Unix/Linux
- wordpress
- windows live writer
- backup
---

![Wordpress](/files/wordpress-backup-and-useful-plugins.jpeg)

WordPress功能确实很强，对于个人Blog或是小型的内容网站非常够用了。WordPress是免费的真是令人感动~~

一、备份Wordpress
===============================

天有不测风云，定期备份是王道，丢了数据可就哭不回来了 :)

要备份两部分的数据

- Wordpress Web目录下文件
- MySQL数据库

备份Dump出来，通过Mail发到自己的邮箱中；再把备份脚本配到Cron上，这样就可以定期备份了，比如每周一6点执行备份。费话少说，上备份脚本：

{% highlight bash linenos %}
#!/bin/bash
# file name: wp-backup.sh
# Author: Jerry Lee (http://oldratlee.github.io)
# Date: 2010-9-27
# Usage: backup wordpress doc and mysql schema data
# set the root directory of your wordpress
WP_ROOT_DIR=/path/to/your/wordpress/root
# set the mysql database info of your wordpress schema
WP_DB_USER=YOUR_DB_USER_HERE
WP_DB_PASSWD=YOUR_DB_PASSWORD_HERE
WP_DB_SCHEMA=YOUR_DB_SCHEMA_NAME_HERE
# set your mail address to receive backup files
# if have more then one address, splite them with space
WP_BACKUP_MAIL="fool@163.com fool@gmail.com"
#######################################
BKP_NOW=`date +%Y%m%d_%H%M%S`
WP_BKP_FILE_NAME=/tmp/wp-"$BKP_NOW".tar.gz
WP_DB_BKP_FILE_NAME=/tmp/wp-"$BKP_NOW".sql.gz
TICK=$SECONDS
tar cfz $WP_BKP_FILE_NAME -C `dirname $WP_ROOT_DIR` `basename $WP_ROOT_DIR`
echo tar wp root dir finished.\($(($SECONDS - $TICK))s\)
TICK=$SECONDS
mysqldump -u$WP_DB_USER -p$WP_DB_PASSWD --database $WP_DB_SCHEMA | gzip > $WP_DB_BKP_FILE_NAME
echo mysqldump wp db finished.\($(($SECONDS - $TICK))s\)
TICK=$SECONDS
MAIL_CONTENT="wordpress root doc: $WP_ROOT_DIR
wordpress database schema: $WP_DB_SCHEMA
uname: `uname -a`
ifconifg:
`/sbin/ifconfig`"
echo "$MAIL_CONTENT" | mutt -s "wordpress backup $BKP_NOW" -a $WP_BKP_FILE_NAME -a $WP_DB_BKP_FILE_NAME -- $WP_BACKUP_MAIL
echo send mail finished.\($(($SECONDS - $TICK))s\)
rm $WP_BKP_FILE_NAME $WP_DB_BKP_FILE_NAME
{% endhighlight %}

把脚本中开头的6个变量设置成你环境中的值。

注意上面的备份过程用到了命令

- mysqldump
- mutt (怎么配置，参见使用mutt命令发送邮件)

确保你的系统上是有的。

记得把脚本配置到系统的crontab上。

二、推荐的插件
========================

1. Add Post URL  
在文章的开始或是结尾加上一文字，比如版权申明、转载链接之类的。  
\# 这个插件我也装了，在文章的开头加了转载链接。
2. Syntax Highlighter ComPress  
程序员常常在文章中贴代码，这个是用来插入代码的。会在WordPress的Blog编辑器加上一个按钮来插入代码。
3. TinyMCE Advanced  
用来加强WordPress的Blog编辑器，可以加上按钮。  
有个选项很有用，设置WordPress不要自动去掉空行。
4. TinyMCE Excerpt   
给摘要的编辑框上也加上内容编辑框上的按钮。
5. WP-reCAPTCHA    
给Blog文章的评论Form加上验证码。免得有人在你的Blog猛发垃圾哦。
6. Login reCAPTCHA   
在登录Blog的Form上加上验证码。
7. 使用windows live writer离线发布Wordpress日志  
这不是一个插件，是个用法，还是不错的。 怎么用参见[用Windows live writer离线发布Wordpress日志](http://chenjinghua.net/windows-live-writer-for-wordpress-314.html)
