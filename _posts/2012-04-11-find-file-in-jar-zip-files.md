---
layout: post
title: 在多个Jar(Zip)文件查找Log4J配置文件的Shell命令行
location: Hangzhou
permalink: /458/tech/shell/find-file-in-jar-zip-files.html
write-time: 2012-04-11 22:37
tags:
- XX
- YY
---


开发时会碰到

- 为什么没有期望的日志输出
- 生效的日志配置文件是哪个

的问题，就要排查Lib目录下哪个Jar文件中有log4j.xml或是log4j.properties配置文件（log4j.xml文件优于log4j.properties生效）。理想情况是只会有一个log4j配置文件，实际上常常在二方库中也有。

然后看看里面的配置，必要的时候要删除或是修改。

实现一
==================

```bash
find -iname '*.jar' | while read jarfile
do
        jar tf $jarfile | grep 'log4j\.properties\|log4j\.xml' | while read item
        do
                echo "$jarfile"\!"$item"
        done
done
```

即用Find命令找出Jar文件，用jar命令list中Jar中文件名，grep到log4j.properties后，输出成

```bash
path/to/file.jar!path/to/log4j.properties
```

的格式，jar文件和jar里面的文件用!分隔。

这里使用jar tf来列出Jar文件中的文件名，也可以使用unzip –l命令。Jar文件即是Zip格式。

但是zip -l列出的输出格式是

```bash
$ unzip -l foo.jar
Archive:  foo.jar
  Length      Date    Time    Name
---------  ---------- -----   ----
        0  2012-01-11 18:55   META-INF/
      571  2012-01-11 18:55   META-INF/MANIFEST.MF
        0  2012-01-11 18:55   META-INF/maven/
      165  2012-01-11 18:55   META-INF/INDEX.LIST
......
---------                     -------
     3430                     8 files
```

我还没有找到不输出头和尾信息的选项。这里要组合一些命令来去了。

实现二
=================

```bash
find -iname '*.jar' -exec sh -c \
    'jar tf $0 | grep 'log4j\.properties\|log4j\.xml' | xargs -r printf "$0!%s\n"' {} \;
```

输出和实现一相同，要短一些，但可读性差些。

这个实现有些潜在的问题：（上面的实现一中没有这些问题）

如果Jar文件中grep的文件名很多，printf命令就会因参数过多而执行失败了。
如何Jar文件名中包含如 %s， printf命令的输出就不对了（需要完整的转义）

后话：
===============

可以把实现做成脚本，加上脚本参数，方便复用执行，如：把要找的Jar文件名模式，在Jar文件的文件夹，Jar文件里要查找文件名的模式，等等。