---
title: JVM五.类加载器
date: 2018-08-08 19:38:31
tags: JVM
categories: JVM
---
博主最近复习深入理解JVM一书，整理归纳，以形成系统认识和方便日后复习。
本文主要介绍
1.  类加载器分类
2. 双亲委派模型

<!-- more -->

# 总览

## 定义
虚拟机设计团队把类加载阶段中的“通过一个类的全限定名来`获取`描述此类的`二进制字节流`”这个动作放到Java虚拟机外部去实现，以便让程序自己决定如何获取所需的类。实现这个动作的代码模块称为**类加载器**

## 类与类加载器
Java中的任意一个类的唯一性，都需要加载他的类加载器和这个类本身一同确定。，每一个类加载器，都拥有一个独立的类名称空间。

换句话说，判断两个类是否“相等”，只有在这两个类都是由同一个类加载器加载的前提下才有意义。否则，即使类的来源是同一个Class文件，被同一个虚拟机加载，只要加载他们的类加载器不同，那这两个类就必定不相等。 

##  类加载器分类
- 从Java虚拟机的角度来讲，只存在两种不同的类加载器：
一种是启动类加载器（Bootstrap ClassLoader），这个类加载器使用C++语言实现，是`虚拟机自身的一部分`
另外一种就是其他的类加载器，这些类加载器都由Java语言实现，`独立于虚拟机外部`，并且都继承自抽象java.lang.ClassLoader。 

- 在Java开发人员的角度来看，类加载器可以大致划分为以下三类：

### 启动类加载器：Bootstrap ClassLoader
- 负责加载存放在`JDK\jre\lib`(JDK代表JDK的安装目录，下同)下，或被`-Xbootclasspath`参数指定的路径中的
- 能被虚拟机识别的类库
  - 如rt.jar，所有的java.开头的类均被Bootstrap ClassLoader加载
  - 名字不符合的类库即使放在 lib 目录中也不会被加载
- 启动类加载器无法被 Java 程序直接引用，用户在编写自定义类加载器时，如果需要把加载请求委派给启动类加载器，直接使用 null 代替即可。

### 扩展类加载器：Extension ClassLoader
该加载器由sun.misc.Launcher$ExtClassLoader实现，它负责加载`JDK\jre\lib\ext`目录中，或者由`java.ext.dirs系统变量`指定的路径中的所有类库（如javax.开头的类），开发者可以直接使用扩展类加载器。

### 应用程序类加载器：Application ClassLoader
该类加载器由sun.misc.Launcher$AppClassLoader来实现，它负责加载`用户类路径（ClassPath）`所指定的类，开发者可以直接使用该类加载器，如果应用程序中没有自定义过自己的类加载器，一般情况下这个就是程序中默认的类加载器。

### 一个小例子

```java
public class ClassLoaderTest {
    public static void main(String[] args) {
       ClassLoader loader = Thread.currentThread().getContextClassLoader();
       System.out.println(loader);
       System.out.println(loader.getParent());
       System.out.println(loader.getParent().getParent());
   }
```

运行后，输出结果：

![类加载器结果](http://p7vxw6hv7.bkt.clouddn.com/18-8-1/86737414.jpg)

从上面的结果可以看出，并没有获取到ExtClassLoader的父Loader，原因是Bootstrap Loader（引导类加载器）是用C++语言实现的，找不到一个确定的返回父Loader的方式，于是就返回null。

# 双亲委派模式(Parents Delegation Model)
应用程序都是由三种类加载器相互配合进行加载的，如果有必要，还可以加入自己定义的类加载器。JVM在加载类时默认采用的是双亲委派机制

## 定义

![类加载器间的关系](http://p7vxw6hv7.bkt.clouddn.com/18-8-1/77928051.jpg)

通俗的讲，就是
1. 某个特定的类加载器在接到加载类的请求时，不会首先尝试加载这个类，而是将加载任务**委托给父类**加载器
2. **每一层**的类加载器都是如此。依次递归 (本质上就是loadClass函数的递归调用)。因此，所有的加载请求最终都应该传送到顶层的启动类加载器中。
3. 如果父类加载器可以完成这个类加载请求，就成功返回；**只有当父类加载器无法完成此加载请求时，子加载器才会尝试自己去加载**

事实上，大多数情况下，越基础的类由越上层的加载器进行加载，因为这些基础类之所以称为“基础”，是因为它们总是作为被用户代码调用的API（当然，也存在基础类回调用户用户代码的情形）。

双亲委派模式要求除了顶层的启动类加载器外，其余的类加载器都应当有自己的父类加载器，双亲委派模式中的父子关系并非通常所说的类继承关系，而是采用**组合关系**来复用父类加载器的相关代码

## 双亲委派模式优势

### 防止内存中出现多份同样的字节码
采用双亲委派模式的是好处是Java类随着它的类加载器一起具备了一种带有**优先级的层次关系**，通过这种层级关可以避免类的重复加载，当父亲已经加载了该类时，就没有必要子ClassLoader再加载一次。

### 保证Java程序安全稳定运行
其次是考虑到**安全因素**，java核心api中定义类型不会被随意替换，假设通过网络传递一个名为java.lang.Integer的类，通过双亲委托模式传递到启动类加载器，而启动类加载器在核心Java API发现这个名字的类，发现该类已被加载，并不会重新加载网络传递的过来的java.lang.Integer，而直接返回已加载过的Integer.class，这样便可以防止核心API库被随意篡改。

参考文章
周志明，深入理解Java虚拟机：JVM高级特性与最佳实践，机械工业出版社
[深入理解Java类加载器(ClassLoader)](https://blog.csdn.net/javazejian/article/details/73413292)
[jvm系列(一):java类的加载机制](http://www.ityouknow.com/jvm/2017/08/19/class-loading-principle.html)







