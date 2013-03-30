---
layout: post
title: Java内部类(Inner Class)小记
location: Hangzhou
permalink: /516/tech/java/java-inner-class-tips.html
write-time: 2012-05-31 21:00
tags:
- tips
- inner class
- 内部类
- Java
---

一、引子
==================

看到Trinea的博文Junit单测代码中java序列化失败的解决，让我想到Java内部类的一些小Gocha，初学Java时很迷惑。这里记录一下。

就以Trinea的博文中的序列化失败的例子做为引子吧。

方便演示先准备一个工具方法：

{% highlight java %}

package com.oldratlee.io.s;
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.ObjectOutputStream;
public class Util {
    public static  void writeObject(T data) throws IOException {
        ObjectOutputStream out = null;
        try {
            out = new ObjectOutputStream(new ByteArrayOutputStream());
            out.writeObject(data);
        } finally {
            if (null != out) {
                try {
                    out.close();
                } catch (Exception e) {
                }
            }
        }
    }
}
{% endhighlight %}

下面的测试运行失败： 

{% highlight java %}

package com.oldratlee.io.s;
import java.io.Serializable;
import org.junit.Test;
public class InnnerClassSerialization_FailTest {
    interface GetDataInterface extends Serializable {
        public Object getData();
    }
    GetDataInterface attributeData = new GetDataInterface() {
           private static final long serialVersionUID = 1L;
           public Object getData() {
               return null;
           }
       };
    public class InnerClassSerialize implements Serializable {
        private static final long serialVersionUID = 1L;
    }
            
    @Test
    public void testSerializable_Attribute_AnonymousClass() throws Exception {
        Util.writeObject(attributeData);
    }
            
    @Test
    public void testSerializable_LocalVar_AnonymousClass() throws Exception {
        GetDataInterface local = new GetDataInterface() {
            private static final long serialVersionUID = 1L;
            public Object getData() {
                return null;
            }
        };
                
        Util.writeObject(local);
    }
    @Test
    public void testSerializable_InnerClass() throws Exception {
        InnerClassSerialize data = new InnerClassSerialize();
        Util.writeObject(data);
    }
}
{% endhighlight %}

上面的3个测试用例全部运行失败。

出错异常相同:

{% highlight java %}

java.io.NotSerializableException: com.oldratlee.io.s.InnnerClassSerialization_FailTest
    at java.io.ObjectOutputStream.writeObject0(ObjectOutputStream.java:1164)
    at java.io.ObjectOutputStream.defaultWriteFields(ObjectOutputStream.java:1518)
    at java.io.ObjectOutputStream.writeSerialData(ObjectOutputStream.java:1483)
    at java.io.ObjectOutputStream.writeOrdinaryObject(ObjectOutputStream.java:1400)
    at java.io.ObjectOutputStream.writeObject0(ObjectOutputStream.java:1158)
    at java.io.ObjectOutputStream.writeObject(ObjectOutputStream.java:330)
    at com.oldratlee.io.s.Util.writeObject(Util.java:12)
    at com.oldratlee.io.s.InnnerClassSerialization_FailTest.testSerializable_Attribute_AnonymousClass(InnnerClassSerialization_FailTest.java:26)
        ......
{% endhighlight %}

异常信息里，可以看到报的类com.oldratlee.io.s.InnnerClassSerialization_FailTest类不可序列化。

三个Test Case序列化是操作没有显式涉及这个类，为什么序列化时要用到这个类呢？后说到内部类的特性会给出原因。在本文的最后一节给出了解决方法。

二、内部类特性

1) 内部类和外部类可以互相访问所有成员（属性&方法）

示例如下，只演示了可以互相访问Private属性。

{% highlight java %}

package com.oldratlee.io.s;
public class A {
    private int attrib;
    private static int staticAttrib;
    public class Inner1 {
        private int a1;
        public int method1() {
            return attrib;
        }
    }
    public static class StaticInner2 {
        private int sa1;
        public int method2() {
            return staticAttrib;
        }
    }
    public int method() {
        int a = new Inner1().a1;
        a += new StaticInner2().sa1;
        return a;
    }
}
{% endhighlight %}

2) 非静态内部类隐含了一个指向外部类引用

内部类实例通过这个指向外部类引用，与外部类实例关联起来，才能够访问外部类实例的成员。

可以通过 外部类名.this 来在内部类中访问这个引用。当内部类的成员和外部类成员重名时，就通过外部类引用来访问外部类的成员；不重名的外部类属性可以省去写上这个引用。

代码示例：

{% highlight java %}

package com.oldratlee.io.s;
public class B {
    private int foo;
    private int age;
            
    public class Inner {
        private int foo;
                
        public int method() {
            return foo + B.this.foo + age;
        }
    }
}
{% endhighlight %}

这一点就是引子里，UT运行失败的原因。

3) 静态成员没有隐含了一个指向外部类引用

 

4) 匿名内部类如何声明静态

 

三、内部类使用方法

 

 

 

四、内部类的使用场景

 

五、总结

 

六、引子问题的解决

1) 修改外部类

外部类（UT类）实现了Serializable接口，这样外部类可以序列化，下面UT通过。

{% highlight java %}

package com.oldratlee.io.s;
import java.io.Serializable;
import org.junit.Test;
public class InnnerClassSerializationTest implements Serializable {
    private static final long serialVersionUID = 1L;
    interface GetDataInterface extends Serializable {
        public Object getData();
    }
    GetDataInterface attributeData = new GetDataInterface() {
           private static final long serialVersionUID = 1L;
           public Object getData() {
               return null;
           }
       };
    public class InnerClassSerialize implements Serializable {
        private static final long serialVersionUID = 88940079192401092L;
    }
            
    @Test
    public void testSerializable_Attribute_AnonymousClass() throws Exception {
        Util.writeObject(attributeData);
    }
            
    @Test
    public void testSerializable_LocalVar_AnonymousClass() throws Exception {
        GetDataInterface local = new GetDataInterface() {
            private static final long serialVersionUID = 1L;
            public Object getData() {
                return null;
            }
        };
                
        Util.writeObject(local);
    }
    @Test
    public void testSerializable_InnerClass() throws Exception {
        InnerClassSerialize data = new InnerClassSerialize();
        Util.writeObject(data);
    }
}
{% endhighlight %}

2) 修改内部类成为静态

这样就不会引用外部类，没有报外部类没有序列化的异常。

演示了声明静态内部类的方法：

非匿名内部类，在类声明上加上static关键字即可
匿名内部类，a) 类属性用到匿名内部类时，把类属性声明成静态 b) 方法里用到匿名内部类时，把方法声明成静态  
\# 如果类属性、方法不是静态的，生成匿名内部类则不是静态类。

{% highlight java %}

package com.oldratlee.io.s;
import java.io.Serializable;
import junit.framework.TestCase;
public class StaticClassSerialization_JUnit3_Test extends TestCase {
            
    interface GetDataInterface extends Serializable {
        public Object getData();
    }
    static GetDataInterface getData = new GetDataInterface() {
        private static final long serialVersionUID = 1L;
        public Object getData() {
            return null;
        }
    };
            
    public static class ClassImplSerialize implements Serializable {
        private static final long serialVersionUID = 88940079192401092L;
    }
            
    public void testSerializable_Attribute_StaticAnonymousClass() throws Exception {
        Util.writeObject(getData);
    }
    public static void testSerializable_LocalVar_StaticAnonymousClass() throws Exception {
        GetDataInterface local = new GetDataInterface() {
            private static final long serialVersionUID = 1L;
            public Object getData() {
                return null;
            }
        };
                
        Util.writeObject(local);
    }
            
    public void testSerializable_StaticInnerClass() throws Exception {
        ClassImplSerialize data = new ClassImplSerialize();
        Util.writeObject(data);
    }
}
{% endhighlight %}

【注】这里使用了JUnit 3，因为JUnit 3允许UT方法为静态；JUnit 4的UT方法，不能为静态，运行时会抛Exception中止。

<EOF>