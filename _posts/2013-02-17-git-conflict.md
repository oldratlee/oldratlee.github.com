---
layout: post
title: Git冲突分析和处理
location: Hangzhou
permalink: /post/2013-02-17/git-conflict
write-time: 2013-02-17 17:35
tags:
- git
- 冲突
- merge
- rebase
- 合并
- patch
- 补丁
---

一同事使用`git pull`冲突了，并且之前本地有未提交的修改。

问题变得比较复杂，因为涉及4方面：

- 工作目录的修改
- 暂存区的修改
- merge来的修改
- Merge前的修改

解决方法：

使用`git merge --abort`中止merge。merge manual中说，这条命令会尽力恢复到Merge之前的状态（可能失败！）。

merge manual中有一条警告：

>Warning: Running git merge with uncommitted changes is discouraged: while possible, it leaves you in a state that is hard to back out of in the case of a conflict.

上面的内容就是说：有未提交修改情况下，不要执行merge！遵守这条警告，防患于未然是最好。

遵守这条警告（或说是最佳实践）后，就只有两方面：

- merge来的修改
- Merge前的修改

关于这种情况下的冲突解决，[《Git权限指南》](http://book.douban.com/subject/6526452/)一书中有比较系统的说明。

这条警告说得更通用些，就是

> 有未提交修改情况下，不要执行可能会冲突的操作！

哪些操作会导致冲突呢？

冲突的来源
========================

很多命令都可能出现冲突。但冲突的直接来源是`merge`和`patch`（应用补丁）。其它的命令是执行这两个操作导致冲突。

- `rebase`：重新设置基准，然后应用补丁。
- `pull`：会自动merge
- `repo sync`：会自动`rebase`
- `cherry-pick`：会应用补丁
- ……

最佳实践
====================

在执行可能有冲突的操作前，先查看一下 **暂存区** 和 **工作目录**，保证其中没有修改。

比如使用`git stash`就可以把**暂存区** 和 **工作目录**的修改保存起来，让**暂存区** 和 **工作目录**处于干净的状态。

遗留的的问题
===================

如果上面最佳实践是合理的，那么应该加上`Hook`，在执行在执行可能有冲突的操作时，检查是否有没提交的修改。如果有，则给出警告终止操作！

<!--excerpt-->

参考资料
==================

- [Git下的冲突解决](http://www.cnblogs.com/sinojelly/archive/2011/08/07/2130172.html)  
这篇博文给出了“冲突来源”的说明，并简单说了冲突的处理。
- 我的一条讨论Git冲突的微博 <http://weibo.com/1836334682/zgQ3y5AzK>

![Why Complex is Easy, Simple is Hard](http://m3.img.libdd.com/farm5/2013/0217/17/9F546A1E5DABE569EEAE4D7C1A8E573128D163801667C_404_297.JPEG)