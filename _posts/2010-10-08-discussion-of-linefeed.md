---
layout: post
title: 如何换行引发的讨论（Unix风格的例子）
location: Hangzhou
permalink: /218/tech/unix/discussion-of-linefeed.html
write-time: 2010-10-08 11:47
tags:
- JLS
- 生活
- blog
- Java,
---

引子1
==================

把多个结果按行输出结果；输出最后一个结果后不要换行。

先不要往下面答案，实现这个很是简单的，自己在纸上写一个伪代码，看看写代码是不是规整。

代码一：

```C
int len = 10;
int result[] = XXX;
for(int i = 0; i < len; ++i) {
    printf(“%d”, i);
    if(i != len –1) { // 如果不是最后一个
        printf(“\n”);
    }
}
```

代码二：

```C
int len = 10;
int result[] = XXX;
for(int i = 0; i < len; ++i) {
    if(i != 0) { // 如果不是第一个
        printf(“\n”);
    }
    printf(“%d”, i);
}
```

这两种写法，倾向于第二种，看起来更整齐些。

其实这两种写法都是琐碎。为什么不用下面的代码呢：

代码三：

```C
int len = 10;
int result[] = XXX;
for(int i = 0; i < len; ++i) {
    printf(“%d”, i);
    printf(“\n”);
} 
```

但是这个代码不符合题目的要求，因为最后一个结果也输出了换行。

更规整的代码是不是反映了与之对应的约定是更合理的呢？

这个问题后面我们会看到答案。


引子2
======================


在Linux下，下面命令

```bash
$ echo -n hello | wc -l
```

的输出是什么？

自己先猜测一下。再到Linux系统运行一下，看看结果。

引子3
=================

用VI编辑器，只输入hello一个词，保存文件退出。

假设文件名是text.txt，下面的命令

```bash
$ wc -c text.txt
```

的输出是什么？

自己先猜测一下。再到Linux系统运行一下，看看结果。

结论
==================

在引子1中看到，关于换行的问题，如果约定成：

每个结果输出都换行。

那么可以得到最规整的代码实现。在Unix系统中，关于换行使用的就是这个约定。在引子2，引子3中可以得到验证。
