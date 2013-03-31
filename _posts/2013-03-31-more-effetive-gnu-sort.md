---
layout: post
title: More Effective Sort
location: Hangzhou
permalink: /post/2013-04-01/more-effective-gnu-sort
write-time: 2013-03-31 22:17:33
tags:
- sort
- gnu
- effective
- shell
---

`sort`用来排序，缺省是对整行文件进行排序。比较难于理解的是Sort支持指定字段排序。

sort支持字段比较，用好这个功能常常可以省去比如用`awk`来截取字段再排序这样的操作，大大地简化操作。

先上示例
===========

先看一完例子，有了功能上的认识之后，再看【关于sort的字段】一节的说明。

第二字段作为Key排序
-------------------

{% highlight bash %}
# 命令行
sort -k2,2 

# 输入
1 c x
2 b y
3 a z
4 a w

# 输出，第一、三个字段不影响
3 a z
4 a w
2 b y
1 c x
{% endhighlight %}

第二字段作为Key排序，从第3个字段开始算（即忽略这个字段的前面2个字符）
-------------------

{% highlight bash %}
# 命令行
sort -k2.3,2

# 输入，和前面的例子相同
1 1c x
2 2b y
3 3a z
4 4a w

# 输出，忽略第二字段的前2个字符（第一字符是空格）
3 3a z
4 4a w
2 2b y
1 1c x
{% endhighlight %}

忽略字段开头的空白
-------------------------

缺省字段的值是包含前面空白的。即这样当分隔字段的空白不一致时（有使用Tab、有一个空格，有2个空格），排序就乱了。

选项`-b, --ignore-leading-blanks`即忽略字段开头的空白。字段声明中可以有选项。

{% highlight bash %}
# 命令行，对第二字段为Key排序
sort -k2,2

# 输入
1  c x
2 b y
3  a z
4 a w

# 输出，有空格的排在前面
3  a z
1  c x
4 a w
2 b y

# 命令行，对第二字段为Key排序，使用选项b
sort -k2b,2

# 输出，字段的开头的空白不影响排序了
3  a z
4 a w
2 b y
1  c x
{% endhighlight %}

多个排序Key
------------------

{% highlight bash %}
# 命令行，第二字段为第一Key排序，第三字段到行尾的内容为第二Key
sort -k2b,2 -k4,4

# 输入
1  c x
2 b y
3  a z
4 a w
5 a x

# 输出，有空格的排在前面
4 a w
5 a x
3  a z
2 b y
1  c x
{% endhighlight %}

有用的排序选项
==============

调整输出
------------

- `-r, --reverse`  
反序输出
- `-u, --unique`  
去重。即如果有多个相同的，只输出一个。
- `-b, --ignore-leading-blanks`  
忽略开头的空白
- `-R, --random-sort`  
随机排序，效果上不是排序，是打乱。  
\# 实际上是以内容的Hash值来排序。

排序方式
-------------

- `-n, --numeric-sort`  
按数据类型排序，`1 < 02 < 3`。  
如果按字符排序，则是 `02 < 1 < 3`。
- `-g, --general-numeric-sort`  
按数据类型排序，支持通用记数，即认识`1.234E10`。要更慢并且有舍入问题（可能`1.2345678 > 1.2345679`）     
详见[What's the difference between --general-numeric-sort and --numeric-sort options in gnu sort](http://stackoverflow.com/questions/1255782/whats-the-difference-between-general-numeric-sort-and-numeric-sort-options/1255800#1255800)
- `-V, --version-sort`  
以版本号的方式排序。这个功能很霸气啊！
- `-M, --month-sort`  
以月份的方式排序。 `(unknown) < JAN < ... < DEC`
- `-h, --human-numeric-sort`  
数据值排序，识别K M G后缀，如2K 1G 1.1M


关于sort的字段
==================

选项是`-k, --key=KEYDEF`。

`KEYDEF = pos1[,pos2]`。

表示以行的第pos1字段到第pos2字段（包含）作为排序的Key。

字段的格式是`F[.C][OPTS]`，F是第几字段，C是从第几个字符开始（即忽略这个字符前面字符的差异）。字段描述两部分，开始字段和结束字段。

`F`和`C`都是从1开始。`pos2`字段的`C`为0时，表示是这个字段的最后一个字符。

`pos``字段的`C`缺省是1，`pos2`字段的`C`缺省是0。

参考资料
===============

- gnu manual [7.1 sort: Sort text files](http://www.gnu.org/software/coreutils/manual/html_node/sort-invocation.html)
- [What's the difference between --general-numeric-sort and --numeric-sort options in gnu sort](http://stackoverflow.com/questions/1255782/whats-the-difference-between-general-numeric-sort-and-numeric-sort-options)
