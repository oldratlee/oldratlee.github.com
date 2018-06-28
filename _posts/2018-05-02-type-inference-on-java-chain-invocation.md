---
layout: post
title: Java链式调用上的类型推导
location: Hangzhou
permalink: /post/2018-05-02/type-inference-on-java-chain-invocation
write-time: 2018-05-02 21:25:57
tags:
- java
- type
- type-inference
- china-invocation
---

> [JEP 101: Generalized Target-Type Inference](http://openjdk.java.net/jeps/101) （`Java 8`的`JEP`），即广义（推广）的目标类型推导。
>
>提出了2个推广加强的Case：
>
> 1. Inference in argument position，在参数位置（提取<font color="blue">形参</font>的类型信息）的类型推导
> 1. Inference in chained calls，链式调用上的类型推导

`Java 8/9(9.0.4)`   <font color="red">目前都不支持</font> <font color="blue"> 在<b>链式调用</b>上做类型推导</font>。

# 测试分析以及结论与解决方法

为了简化代码及其类型推导的分析，选用`List`，在方法上完全不涉及类型上下限，涉及方法及其签名如下：

\# 与在[JEP 101: Generalized Target-Type Inference](http://openjdk.java.net/jeps/101)中对链式调用上的类型推导所用的示例（`List.nil().head()`）一致。因为`Java`的`List`没有`head`方法，用功能和泛型参数一样的`get(0)`方法替代。

```java
// java.util.Collections#emptyList
public static final <T> List<T> emptyList();

// java.util.List#get
E get(int index);
```

## 测试代码

```java
import java.util.Collections;
import java.util.List;

public class TypeInferenceShowcase {
    public static void main(String[] args) {
        // Compile OK
        List<String> list1 = Collections.emptyList();
        String head1 = list1.get(0);

        // write as chained call.
        // Compile error!!
        String head = Collections.emptyList().get(0);
    }
}
```

## 编译结果

`Java 8/9` 的编译出错信息完全一样。

```bash
## Java 8 ##
$ javac -version && echo && javac TypeInferenceShowcase.java
javac 1.8.0_162

TypeInferenceShowcase.java:12: 错误: 不兼容的类型: Object无法转换为String
        String head = Collections.emptyList().get(0);
                                                 ^
1 个错误

## Java 9 ##
$ javac -version && echo && javac TypeInferenceShowcase.java
javac 9.0.4

TypeInferenceShowcase.java:12: 错误: 不兼容的类型: Object无法转换为String
        String head = Collections.emptyList().get(0);
                                                 ^
1 个错误
```

## 解决方法

因为`Java 8/9`  <font color="red">不支持</font> <font color="blue"> 在<b>链式调用</b>上做类型推导</font>，  
\# 即 <font color="red">不支持</font> 类型信息从链路后面的调用 向 前面 传递，即 支持在调用链上类型信息的<font color="blue"><b>逆向传递</b></font>  
所以需要自己在 **链式调用** 上手动补上类型信息：

```java
String head = Collections.<String>emptyList().get(0);
//                         ^^^^^^
```

实际上，在`IntelliJ IDEA`中，执行变量`list1`内联，生成的就是上面的代码；`IntelliJ IDEA`都帮我们处理好了。具体的操作方法是：

```java
List<String> list1 = Collections.emptyList();
//            ^ 光标放list1变量上（上面或下面的都行），执行【Refactor - Inline...】(Alt+Cmd+N)，见下图
String head1 = list1.get(0);
```

![image.png](http://ata2-img.cn-hangzhou.img-pub.aliyun-inc.com/0b7ea03a3bd34e8e280a58ff81ebb2f1.png)

# 展开分析与总结梳理

[JEP 101: Generalized Target-Type Inference](http://openjdk.java.net/jeps/101) （`Java 8`的`JEP`），即广义（推广）的目标类型推导。

提出了2个推广加强的Case：

1. Inference in argument position，在参数位置（提取<font color="blue">形参</font>的类型信息）的类型推导
    * 类型信息 从嵌套调用的 外面调用 向 里面的调用 传递，即 支持在<b>嵌套调用</b>时 类型信息的 <font color="blue"><b>由外往里 的逆向传递</b></font>。
    * 在`JDK 8`之前，只有在 **_赋值语句_** 中 会做这样类型推导（提取<font color="blue">赋值变量</font>的类型信息）  
      虽然在参数位置上，其实可以看作是 <font color="blue"><b><i>实际参数（实参）</i></b>给<b><i>形式参数（形参）</i></b>的<b><i>赋值</i></b></font> 
2. Inference in chained calls，链式调用上的类型推导
    * 类型信息从调用链 后面的调用 向 前面的调用 传递，即 支持**链式调用**时 类型信息的 <font color="blue"><b>由后往前 的逆向传递</b></font>。
    * 这个Case，可以看作是 赋值语句推广加强  
         因为链式只有一级调用时，就 <font color="blue"><b>退化</b></font>成了 赋值语句 的Case。
    * `JEP`中说的好好的，然而，上面的测试可以看到，`Java 8/9` <font color="red"><b>目前是不支持的</b></font> ！！

总得来说，`Java`的类型推导还是不够友好的，这些我们人肉解析器都能在直觉上很快完成推导觉得没问题的代码，`Java`编译却是过不了，**surprise～** =_=！

不过，实际业务使用和工程应用上，这<font color="green"><b>并不是多大的问题</b></font>，因为 需要 在调用链上 类型信息的 <font color="blue">由后往前 的逆向传递</font> 的情况其实很少。

说到类型推导能力的强弱，我不得不想到邻家小伙`Scala`，相比起来做得就是到位贴心了。**Just no surprise!**

# 邻家小伙`Scala`的类型推导能力对比

链式调用上的类型推导，对于`Scala`是个小Case，毫无压力。对等的测试如下。

## 测试代码

```java
class TypeInferenceScalaShowcase {
  def main(args: Array[String]): Unit = {
    // write as chained call.
    // Compile OK
    val head: String = List.empty.head
  }
}
```

## 编译结果

```bash
$ scalac -version && echo && scalac TypeInferenceScalaShowcase.scala && echo 'Compile Success!'
Scala compiler version 2.12.4 -- Copyright 2002-2017, LAMP/EPFL and Lightbend, Inc.

Compile Success!
```

妥妥的！～  :)

# 相关阅读/资料

- [Generic type inference not working with method chaining? - stackoverflow.com](https://stackoverflow.com/questions/24794924/generic-type-inference-not-working-with-method-chaining)  
    这个`SOF`的问题中也说了： `Java 7/8` <font color="red">都不能</font> 在 **链式调用** 上做类型推导。
