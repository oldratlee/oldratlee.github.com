---
layout: post
title: Java对象初始化顺序
location: Hangzhou
permalink: /576/tech/java/java-object-initialization-order.html
write-time: 2012-07-09 23:22
tags:
- 顺序
- object
- initialization order
- Java
- 初始化
- 对象
- 对象初始化
---

今天我在淘宝Blog阅读了一篇关于java对象实例初始化顺序的文章，讲得挺好的，还严谨地找出了JLS。

觉得Demo代码例子举的不简练，我写了一个Demo代码，狗尾续貂一下 :D

示例代码

{% highlight java %}
package com.oldratlee.initorder;
class Father {
    Object obj1 = new Object() {
        {
            System.out.println("Init Father Attribute!");
        }
    };
    public Father() {
        System.out.println("Run Father Constructor!");
    }
}
public class Son extends Father {
    Object obj2 = new Object() {
        {
            System.out.println("Init Son Attribute!");
        }
    };
    public Son() {
        super(); // 这是缺省构造函数，可以省略这一行
        System.out.println("Run Son Constructor!");
    }
    public static void main(String[] args) {
        new Son();
    }
}
{% endhighlight %}

输出的结果是：

{% highlight java %}

Init Father Attribute!
Run Father Constructor!
Init Son Attribute!
Run Son Constructor!
{% endhighlight %}

总结说明

根据结果总结“java对象实例初始化顺序”如下：

首先，为类分配内存空间（包含基类），初始化所有成员变量为默认值，包括primitive类型(int=0,boolean=false,…)和Reference类型。

然后，

1. 父类初始化
1. 类成员初始化（实际上也含了初始化代码块）
1. 执行构造函数代码

【注】第一点『父类初始化』执行得也是上面的三点（递归）。展开递归过程就是按继承层次，先执行上层基类 “类成员初始化”“构造函数代码”，再执行子类的。最高的基类是Object类（没有类成员，构造函数空）。

PS:  
示例代码在 https://bitbucket.org/oldrat/java-tips/overview 可以下载查看。