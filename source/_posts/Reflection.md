---
title: Reflection
date: 2018-05-02 23:19:33
tags: reflection 
categories: 编程语言
---
Java反射机制是指在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性。这种动态获取的信息以及动态调用对象的方法的功能称为Java语言的反射机制。 
用一句话总结就是反射可以实现在**运行时(Runtime)**知道任意一个类的属性和方法。
<!-- more -->
# 一. 反射的定义
反射(Reflection)是Java 程序开发语言的特征之一，它允许运行中的 Java 程序获取自身的信息，并且可以操作类或对象的内部属性。
Oracle官方对反射的解释是
> Reflection enables Java code to discover information about the fields, methods and constructors of loaded classes, and to use reflected fields, methods, and constructors to operate on their underlying counterparts, within security restrictions.
The API accommodates applications that need access to either the public members of a target object (based on its runtime class) or the members declared by a given class. It also allows programs to suppress default reflective access control.

简而言之，通过反射，我们可以在运行时获得程序或程序集中每一个类型的成员和成员的信息。
程序中一般的对象的类型都是在编译期就确定下来的，而Java反射机制可以动态地创建对象并调用其属性，这样的对象的类型在编译期是未知的。所以我们可以通过反射机制直接创建对象，**即使这个对象的类型在编译期是未知的。**
反射的核心是JVM在运行时才动态加载类或调用方法/访问属性，它不需要事先（写代码的时候或编译期）知道运行对象是谁。

# 二. 反射的重要性
当程序运行时，允许改变程序结构或变量类型，这种语言称为动态语言。我们认为java并不是动态语言，但是它却有一个非常突出的动态相关机制即反射。
IT行业里这么说，没有反射也就没有框架，现有的框架都是以反射为基础。在实际项目开发中，用的最多的是框架，填的最多的是类，反射这一概念就是将框架和类揉在一起的调和剂。所以反射才是接触项目开发的敲门砖。
# 三. 使用反射的流程
根据[官方API](http://www.oracle.com/technetwork/articles/java/javareflection-1536171.html)写就行了，大致流程就是:

1. 获取特定类的**Class类**，即该类对应的字节码，共三种方法：
* 类名.class
* 对象名.getClass()
* Class.forName(“要加载的类全名”)

2. 调用```Class ```对象的```getConstructor(Class<?>... parameterTypes)```获取**构造方法对象**
3. 调用是构造方法类```Constructor```的```newInstance(Object... initargs)```方法**新建**对象
4.  调用```Class```对象的```getMethod(String name, Class<?>... parameterTypes)```获取**方法对象**
5. 调用方法对象类```Method```的```invoke(Object obj, Object... args)```方法，**调用**对象上相应方法
# 四. 反射的原理
## Class类 
#### 类是不是对象?
在面向对象的世界里，一切皆是对象。而在java语言中，有两个东西特殊：static修饰的东西不是对象，但是它属于类；普通的数据类型不是对象，例如：int a = 5;它不是面向对象，但是它有其包装类 Integer 或者分装类来弥补了它。除了以上两种不是面向对象，其余的**包括类也有它的面向对象**，类是java.lang.Class的**实例化对象**（注意**C**是大写）。
#### 可以用new关键字实例化Class对象吗?
对于普通的对象，我们一般都会这样创建和表示(实例化)：
``` 
Code c =new Code();
```
我们会下意识地这样实例化Class类的对象(错误)：
``` 
Class c =new Class();//错误
```
查看Class源码，原因是Class类构造器是私有的，只有JVM可以创建Class的对象，因此不可以像普通类一样new一个Class对象，我们无法在代码中显式地声明一个 Class 对象。
>  Only the Java Virtual Machine creates Class objects.
```
    /*
     * Private constructor. Only the Java Virtual Machine creates Class objects.
     * This constructor is not used and prevents the default constructor being
     * generated.
     */
    private Class(ClassLoader loader) {
        // Initialize final field for classLoader.  The initialization value of non-null
        // prevents future JIT optimizations from assuming this final field is null.
        classLoader = loader;
    }
```
#### 类类型
虽然我们不能用构造器new一个Class对象，但是却可以通过已有的类得到一个Class的 **实例对象**，共有三种方式，如下：
```
1. Class c1 = Code.class;
//这说明任何一个类都有一个  隐含的静态成员变量class，  这种方式是通过获取类的静态成员变量class得到的

2. Class c2 = c.getClass();
//code1是Code的一个对象，这种方式是通过一个类的对象的getClass()方法获得的

3. Class c3 = Class.forName("com.test.reflect.Code");
//这种方法是Class类调用forName方法，通过一个类的全量限定名获得
```

```
System.out.println(c1 == c2)? or System.out.println(c1 == c3)?
```
答案是肯定的，返回值为ture。这表明不论c1 or c2 or c3都代表了F类的类类型，也就是说一个类只可能是Class类的一个实例对象。
c1、c2、c3都是Class的对象，叫做**Code这个类的类类型（Class Type）**。
类类型是反射的基础！
#### 如何实例化Class对象?
已经得到了类类型，可以通过类类型的newInstance()方法创建某个类的对象实例以及调用方法，如下：
```
Code code = (Code)c1.newInstance(); // 必须要有无参的构造器
code.print();
```

参考文章
[浅谈 Java 中的 Class 类](https://blog.csdn.net/my_truelove/article/details/51289217)
[谈谈Java反射机制](https://www.jianshu.com/p/6277c1f9f48d)
[深入解析Java反射（1） - 基础](https://www.sczyh30.com/posts/Java/java-reflection-1/)
[java基础巩固笔记(1)-反射](https://brianway.github.io/2016/01/08/javase-learn-note-1-Reflect/)
[Java反射入门](https://blog.csdn.net/trigl/article/details/51042403)
[Using Java Reflection](http://www.oracle.com/technetwork/articles/java/javareflection-1536171.html)
[慕课网教学](https://www.imooc.com/video/3733/0)