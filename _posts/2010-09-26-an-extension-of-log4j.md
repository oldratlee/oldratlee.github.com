---
layout: post
title: 一个对Log4J的扩展
location: Hangzhou
permalink: /127/tech/java/an-extension-of-log4j.html
write-time: 2010-09-26 19:29
tags:
- extension
- Java
- log4j
---

一、为什么要扩展Log4J
============================

Log4J对日志提供的丰富的功能，有很多选项可以设置。一般来说，我们不需要写自己的Appender或是Layout，只要设置一个Log4J自己的对象就可以了。

应用场景不多种多样的，在做的Napoli的过程中，就发生的一个需要扩展Log4J的场景。

Log4J的Appender有多种，常用的有

- Console Appender，把Log信息打在控制台上
- File Appender，把Log信息打在文件里
- Rolling File Appender，也是打在文件里，可以指定日志文件的大小，超过这个大小，滚动文件，命名成：  
info.log, info.log.1, info.log.2, info.log.3 ....  
info.log是正在往里打日志的文件，后面是日志历史。

在Napoli中打出的日志，要被收集走，集中处理。 所谓的"收集走"是指，被外部程序移走。

**“上面的情况使用Rolling File Appender不行吗？移走日志历史文件，就好了。”**

移走日志历史文件是避免了，移动正在被写日志的文件可能出现的问题，（如可能丢掉 移动过程中的日志信息）

但是在Rolling File Appender会重命名日志的历史文件，当前的日志文件写满时，当前的命名由info.log -> info.log.1, info.log.1 -> info.log.2, info.log.2 -> info.log.3 ......

要在Rolling File Appender重命名日志文件的过程，"收集走"日志文件，就可能出错了。

**“怎么办上面的问题？”**

可以修改一下，当前的日志文件写满时，文件名重命名的规则，在Log4J中这个叫做Rollover（文件滚动）。

当前的日志文件写满时，由当前的命名由info.log -> info.log.20090911223059777，就是在文件后面加上时间截（精确到毫秒），这样产生新的历史文件时，并不需要生命名老的文件。BingGo！

二、如何扩展
===================

这里针对的Log4J的版本是**1.2.15**。

要扩展的Log4J的RollingFileAppender类，定位文件滚动的逻辑，在rollOver方法中：   
\# 这里不详细说明Log4J的实现，怎么找到rollOver方法的。可以下载Log4J的源码，看一下RollingFileAppender类和它的父类

{% highlight java %}
public // synchronization not necessary since doAppend is alreasy synched
void rollOver() {
  File target;
  File file;
  if (qw != null) {
      long size = ((CountingQuietWriter) qw).getCount();
      LogLog.debug("rolling over count=" + size);
      //   if operation fails, do not roll again until
      //      maxFileSize more bytes are written
      nextRollover = size + maxFileSize;
  }
  LogLog.debug("maxBackupIndex="+maxBackupIndex);
  boolean renameSucceeded = true;
  // If maxBackups <= 0, then there is no file renaming to be done.   if(maxBackupIndex > 0) {
    // Delete the oldest file, to keep Windows happy.
    file = new File(fileName + '.' + maxBackupIndex);
    if (file.exists())
     renameSucceeded = file.delete();
    // Map {(maxBackupIndex - 1), ..., 2, 1} to {maxBackupIndex, ..., 3, 2}
    for (int i = maxBackupIndex - 1; i >= 1 && renameSucceeded; i--) {
      file = new File(fileName + "." + i);
      if (file.exists()) {
        target = new File(fileName + '.' + (i + 1));
        LogLog.debug("Renaming file " + file + " to " + target);
        renameSucceeded = file.renameTo(target);
      }
    }
  if(renameSucceeded) {
    // Rename fileName to fileName.1
    target = new File(fileName + "." + 1);
    this.closeFile(); // keep windows happy.
    file = new File(fileName);
    LogLog.debug("Renaming file " + file + " to " + target);
    renameSucceeded = file.renameTo(target);
    //
    //   if file rename failed, reopen file with append = true
    //
    if (!renameSucceeded) {
        try {
          this.setFile(fileName, true, bufferedIO, bufferSize);
        }
        catch(IOException e) {
          LogLog.error("setFile("+fileName+", true) call failed.", e);
        }
    }
  }
  }
  //
  //   if all renames were successful, then
  //
  if (renameSucceeded) {
  try {
    // This will also close the file. This is OK since multiple
    // close operations are safe.
    this.setFile(fileName, false, bufferedIO, bufferSize);
    nextRollover = 0;
  }
  catch(IOException e) {
    LogLog.error("setFile("+fileName+", false) call failed.", e);
  }
  }
}
{% endhighlight %}

上面的代码是从Log4J的源码中拷贝出来的，保留了格式（包括缩进）。

虽然Log4J是Apache的项目，但是这个代码的风格看起来还不是很Java风格的吧 :(

- 有的代码缩进是不正确的。
- 使用了多次的局部变量renameSucceeded、target、file。这些变量含义多变，影响了代码的可读性。（这是老版C的风格！）

我们要改的逻辑是重命名的逻辑，就是下面的一些代码：

{% highlight java %}
// If maxBackups <= 0, then there is no file renaming to be done.   if(maxBackupIndex > 0) {
    // Delete the oldest file, to keep Windows happy.
    file = new File(fileName + '.' + maxBackupIndex);
    if (file.exists())
     renameSucceeded = file.delete();
    // Map {(maxBackupIndex - 1), ..., 2, 1} to {maxBackupIndex, ..., 3, 2}
    for (int i = maxBackupIndex - 1; i >= 1 && renameSucceeded; i--) {
      file = new File(fileName + "." + i);
      if (file.exists()) {
        target = new File(fileName + '.' + (i + 1));
        LogLog.debug("Renaming file " + file + " to " + target);
        renameSucceeded = file.renameTo(target);
      }
    }
  if(renameSucceeded) {
    // Rename fileName to fileName.1
    target = new File(fileName + "." + 1);
    this.closeFile(); // keep windows happy.
    file = new File(fileName);
    LogLog.debug("Renaming file " + file + " to " + target);
    renameSucceeded = file.renameTo(target);
    //
    //   if file rename failed, reopen file with append = true
    //
    if (!renameSucceeded) {
        try {
          this.setFile(fileName, true, bufferedIO, bufferSize);
        }
        catch(IOException e) {
          LogLog.error("setFile("+fileName+", true) call failed.", e);
        }
    }
  }
  }
{% endhighlight %}

改成：

{% highlight java %}
// If maxBackups <= 0, then there is no file renaming to be done.         if (maxBackupIndex > 0) {
            // Delete the oldest file, to keep Windows happy.
            final File logFile = new File(fileName);
            final String logFileName = logFile.getName();
            final File logDir = logFile.getAbsoluteFile().getParentFile();
            List oldFiles = new ArrayList();
            for (File f : logDir.listFiles()) {
                String fileName = f.getName();
                if (fileName.startsWith(logFileName) && fileName.length() > logFileName.length()) {
                    oldFiles.add(fileName);
                }
            }
            Collections.sort(oldFiles);
            for (int i = 0; i < oldFiles.size() - maxBackupIndex; ++i) {
                File f = new File(logDir, oldFiles.get(i));
                renameSucceeded = f.delete();
            }
            // 当前文件重命名
            final String dateSuffix = sdf.format(new Date());
            target = new File(fileName + '.' + dateSuffix);
            file = new File(fileName);
            renameSucceeded = file.renameTo(target);
            if (!renameSucceeded) {
                try {
                    this.setFile(fileName, true, bufferedIO, bufferSize);
                } catch (IOException e) {
                    LogLog.error("setFile(" + fileName + ", true) call failed.", e);
                }
            }
        }
{% endhighlight %}

三、上面的做法有问题
======================

**“你在晃点我啊！？照这上面的改法，是要我直接改Log4J的RollingFileAppender啊？这不叫扩展！”**

:) 被你发现了！

一个扩展的做法，是自己写一个 RollingFileAppender 的子类，只复然后只覆写上面的的逻辑。

要继承类，覆写逻辑的最小单位是 方法，所以覆写上面rollover方法，虽然实际要覆写只是上面列出的逻辑，结果是方法中代码行有很多是重复的。

按照上面的做法，又出现了新的问题。rollover方法用到的成员变量nextRollover是私有的，子类没有办法访问到。

要绕过 成员变量nextRollover不能访问的问题，一个方法是在子类自己建一个nextRollover成员，父类RollingFileAppender中所有用到的nextRollover的方法，在子类拷贝一份。

用到的方法有： rollOver()、subAppend(LoggingEvent)。还好只有两个。

四、扩展的一团糟 带来的启发
===================================

第三节中，给出的解决方法，真是要用"一团糟"来形容

- 大量的重量代码
- 绕过 成员变量nextRollover不能访问的问题，用的猥琐做法

之所以有这些问题，归根结底是因为，Log4J没有把Rollover文件重命名的策略抽出来，没有认识到把可变的策略隔离到方法。

如果独立出这个逻辑成proteceted方法，子类就好扩展了。

当然，根据'组合优于继承'原则， Rollover文件重命名的策略抽成一个成员（即使用策略模式）由外部提供，当然也应该有默认值。这是一个更好的实践！

Log4J的参考资料
==========================

- 官方网站： <http://logging.apache.org/L   
一般用的是1.2的版本，主页在 <http://logging.apache.org/log4j/1.2/index.html>，有API文档，源码下载。。。
- Short introduction： <http://logging.apache.org/log4j/1.2/manual.html>
- FAQ： <http://logging.apache.org/log4j/1.2/faq.html>
- 《Pro Apache Log4j》：2005年出的书，讲的是Log4J 1.2。