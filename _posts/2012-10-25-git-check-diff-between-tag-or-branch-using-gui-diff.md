---
layout: post
title: Git如何使用GUI（图形化）Diff工具查看两个分支或是标签的Diff
location: Hangzhou
permalink: /post/2012-10-25/git-check-diff-between-tag-or-branch-using-gui-diff
write-time: 2012-10-25 0:31
tags:
- XX
- git
- diff
- Beyond Compare
- tag
- branch
- 分支
- 标签
- svn
- gui
- 差异
- GUI Diff
---

使用TortoiseGit可以解决这个问题。

1. 在Git工程目录，右键菜单：  
![TortoiseGit右键菜单](http://m1.img.libdd.com/farm5/2012/1025/00/2708F8323B37A762B14111EF5B85318CC93942F71BE61_390_518.PNG)
2. 点菜单项【Git与前一版本比较】  
如何没有看到这个菜单项，则在【TortoiseGit】的子菜单中。
2. 打开了【TortoiseGit的版本Diff窗口】，缺省显示的是工作区和前一个提交版本的差异  
![TortoiseGit的版本Diff窗口](http://m2.img.libdd.com/farm4/2012/1025/01/7CCDA48095CD133AF102394BAB39C0BB8D37C176D9755_476_477.PNG)
2. 点击右边的按钮，选择要比较的分支或是标签后，下列的文件列表里就是两个分支或是标签的Diff相关的文件。双击里面的文件条目，TortoiseGit会使用TortoiseGit配置的GUI Diff查看工具查看文件Diff。

其实【TortoiseGit的版本Diff窗口】的右边按钮选择，除了选分支或是标签，还可以选择指定版本，很方便。

我用的是Beyond Compare来查看Diff，可以配置成Beyond Compare查看Diff时忽略空白、Java注释之类的不重要的修改，这样可以聚焦到对运行有影响的代码修改上。

这个功能Google了几天没有找到解决方法，最后是把TortoiseGit的菜单项一个一个地过，给找到了。

相信我能想到的需求，TortoiseGit一定是早想到解决了。

下面说一下引发这个问题的的问题。

# 为什么有需求，要查看两个分支或是标签的Diff？

和2个版本并行的开发模式有关，参见我的博文： [Dubbo的2个版本并行的开发模式](http://oldratlee.com/post/2012-11-08/40042657196 "Dubbo的2个版本并行的开发模式")

采用2个版本并行开发后，当新功能版本要成为线上的GA版本时，会想和当前GA比较一下，

* 相对当前的GA版本增减那些内容，会不会有风险？
* 有没有遗漏掉GA的Bug Fix代码Port到新功能版本中？   
虽然Dubbo有Ticket记录，每次发布前做到大家一起Review每个Ticket和修改。   
人总是会出错，这样有发生过。遗漏BugFix的代码可能就是线上的又一次故障！

# 为什么要用GUI Diff？

其实查看两个分支或是标签的Diff，Git命令行就可以做到：

```bash
git diff arg1 arg2
```

上面的给2个参数，可以是要比较的是分支或是标签名。
Git会把Diff都找出来，在less命令中查看。

这个命令行，功能没有问题。一个不方便的地方是less命令会把所有的差异都列出来，比如空白、注释等等。

GUI Diff工具可以配置忽略不重要的异常（比如我用的Beyond Compare），省去不少不必要的时间浪费。

另，GUI的显示会更直观和方便操作，这正是Diff查看需要特征。比如分左右两栏对比查看（VimDiff也可以）、把变动的内容用不同的颜色标识出来 等等。

关于的GUI和CLI的比较可以看一下我另一篇博文[【译】GUI & CLI Principles](http://oldratlee.com/post/2012-11-04/gui-cli-principles "【译】GUI & CLI Principles")。

## 使用git difftool

另一个类似的解决解决方法是使用`git difftool`命令，相对于`git diff`是通过配置的GUI来查看Diff。

```bash
[diff]
    tool = jellybc3
[difftool]
    prompt = false
[difftool "jellybc3"]
    #use cygpath to transform cygwin path $LOCAL (something like /tmp/U5VvP1_abc) to windows path, because bc3 is a windows software
    cmd = \"/cygdrive/c/program files/beyond compare 3/bcomp.exe\" \"$(cygpath -w $LOCAL)\" \"$REMOTE\"
[merge]
    tool = jellybc3
[mergetool]
    prompt = false
[mergetool "jellybc3"]
    #trustExitCode = true
    cmd = \"/cygdrive/c/program files/beyond compare 3/bcomp.exe\" \"$LOCAL\" \"$REMOTE\" \"$BASE\" \"$MERGED\" \"$MERGED\"
```
注意： 

1. cygwin上，$LOCAL不转换为Windows路径，会发现BC只打开了一个文件。
2. 注意引号需要转义，否则git调用时会出错。

参考[Git下使用Beyond Compare作为比较和合并工具](http://www.cnblogs.com/sinojelly/archive/2011/08/07/2130173.html)

# SVN如何使用的图形化查看两个分支或是标签的Diff

顺便也说一下SVN的做法，使用SVN时我也会要这么做。

\# 实际上是SVN这么做了，让我在使用Git时也想到这么做。

1. 在TortoiseSVN的【版本库浏览器】中，在要比较标签或是分支目录上右键，点菜单项【为比较标记】，这样就指定了一个要比较目录。这里我选择的是标签2.4.3，选完后会粗体。  
![选定比较1](http://m1.img.libdd.com/farm5/2012/1025/01/5B7F171EE7740D6CC92FAB843F69578D4FBC88C28CEB9_500_637.jpg)
2. 在另一个要比较的标签或是分支目录上右键，点菜单项【比较URL】，就会打开【已改变文件】的窗口。  
![比较URL](http://m2.img.libdd.com/farm5/2012/1025/01/74BAB510776A85FC7457A334CBA1EB0A172847EEEE2BA_500_756.jpg)
3. 【已改变文件】的窗口。
![已改变文件](http://m3.img.libdd.com/farm4/2012/1025/12/CF7AF482EFF8EDF2CD1239E8E8B8D543C4D986F72BA9D_497_443.PNG)

Thanks for reading, and I hope it can help you :-)