---
title: JVM一.运行时数据区域
date: 2018-07-01 15:32:07
tags: JVM
categories: JVM
---
博主最近复习深入理解JVM一书，整理归纳，以形成系统认识和方便日后复习。
本文主要介绍
1. JVM意义
2. JVM运行时内存分布以及各个区域的作用

<!-- more -->

# Java与JVM

## JVM定义
[百度百科定义](https://baike.baidu.com/item/JVM)：

> JVM是Java Virtual Machine（Java虚拟机）的缩写，是一种用于计算设备的规范，它是一个虚构出来的计算机，是通过在实际的计算机上仿真模拟各种计算机功能来实现的。


## JVM意义---实现平台无关

[百度百科定义](https://baike.baidu.com/item/JVM)：

> Java语言的一个非常重要的特点就是与平台的无关性。而使用Java虚拟机是实现这一特点的关键。一般的高级语言如果要在不同的平台上运行，至少需要编译成不同的目标代码。而引入Java语言虚拟机后，Java语言在不同平台上运行时不需要重新编译。
Java语言使用Java虚拟机屏蔽了与具体平台相关的信息，使得Java语言编译程序只需生成在Java虚拟机上运行的目标代码字节码，就可以在多种平台上不加修改地运行。Java虚拟机在执行字节码时，把字节码解释成具体平台上的机器指令执行。这就是Java的能够“一次编译，到处运行”的原因。

### 机器语言

- 由于计算机**只认识机器语言**(二进制)，所以高级语言(如C、C++、Java、Python等)都不能被计算机直接执行，必须经过**有效翻译**
- 编译和解释都是将高语言**翻译**成机器语言的方式，而区别是**翻译的时间不同**
  - 编译型语言写的程序在执行之前，**先经过编译**过程，把程序编译成机器语言文件，当运行时直接执行，因此只需进行一次翻译
  - 解释型语言写的程序不需要编译，直到程序**运行时**才进行动态翻译后执行

### 翻译的方式

- 编译型语言
源程序经过**编译、汇编和连接**得到输出目标代码，然后由计算机执行目标代码(机器指令)。
代表语言：C 、C++
- 解释型语言
由可以理解源代码的解释程序(解释器)执行，解释器的任务是将源代码(中间代码)解释成可执行的机器指令。 **代码执行时逐一解释**成可执行的机器指令。 
代表语言：Ruby、JS，大多动态语言都是解释型的。

### Java的方式

Java较为特殊
1. Java需要编译，但是没有直接编译成机器语言，而是把`.java`编译成为`.class`的**字节码**文件
2. 通过JVM的解释器对`.class`文件进行解释执行
2.1 在Windows系统上运行，则通Windows上的Jvm进行解释
2.2 在**Linux系统**上，则通过Linux上的Jvm解释执行

![Java的方式](http://upload-images.jianshu.io/upload_images/11861611-105946efda62ae26.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## JVM的定位

![Java体系](http://upload-images.jianshu.io/upload_images/11861611-49491b65a716783f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- Java程序设计语言、Java虚拟机、Java API类库三部分统称为JDK(Java Development Kit) ,JDK是Java程序**开发**的最小环境
- Java API类库中的Java SE API子集和Java虚拟机两部分统称为JRE(Java Runtime Environment)，JRE是支持Java程序**运行**的标准环境

# JVM内存区域

Java虚拟机在执行Java程序过程中，会把所管理的内存划分为几个不同的数据区域（**程序计数器、虚拟机栈、堆、方法区、本地方法栈**），统称为**运行时数据区域**。如下：

![运行时数据区](http://upload-images.jianshu.io/upload_images/11861611-a013fa13eff1368b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![更立体的表示 ](http://upload-images.jianshu.io/upload_images/11861611-ccf1fa09497cd789.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 1. 程序计数器（Program Counter Register）

### 定义
**当前线程**执行代码的**行号指示器**

### 工作原理
- **字节码解释器**通过改变程序计数器的值选取下一条要执行的字节码指令，分支、循环、跳转、异常处理、线程回复等都依赖程序计数器完成。
- 由于Java虚拟机多线程是通过随机切换线程分配处理器执行时间的方式实现的，所以在任一确定时刻，一个处理器（对于多核处理器来说是一个内核）都只会执行一条线程中的指令。为了切换后能正确恢复到原本的执行位置，因此每条线程都需要独立的程序计数器，所以这类区域为“线程私有” 的内存。

### 注意
- 此区域是**唯一**在Java虚拟机规范中没有规定任何`OutOfMemoryError`的区域
- 线程私有
- 如果正在执行的是本地方法则为空


## 2. 虚拟机栈（Java Virtual Machine Stacks）

![JVM Stacks](http://upload-images.jianshu.io/upload_images/11861611-b3230ed99d8ea642.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 定义
描述的是Java方法执行的内存模型

### 具体细节
每个方法在执行的同时都会创建一个栈帧（Stack Frame）用于存储局部变量表、操作数栈、动态链接、方法出口等信息。**每个方法从调用直至执行完成的过程，就对应一个栈帧在虚拟机栈中入栈到出栈的过程。**
一个线程中的方法可能还会调用其他方法，这样就会构成方法调用链，而且这个链可能会很长，而且每个线程都有方法处于执行状态。对于执行引擎来说，只有活动线程`栈顶`的栈帧才是有效的，称为当前栈帧（Current Stack Frame），这个栈帧关联的方法称为当前方法（Current Method）。

### 局部变量表
局部变量表存放了
1. 编译器可知的各种基本数据类型（8种基本数据类型）
2. 对象引用（reference类型，指向堆中对象起始地址的引用指针，也可能是指向代表对象的句柄或对象相关的位置）
3. returnAddress类型


其中64位长度的long和double类型的数据会占用2个局部变量空间（Slot）,其余基本类型只占用1个。
局部变量表所需内存空间在**编译期完成分配**。当进入一个方法时，这个方法需要在帧中分配多大的变量空间是**完全确定的**，在方法运行期间不会改变局部变量表大小。

### 异常
在Java虚拟机规范中，对这个区域规定了两种异常状况
1. 如果线程**请求的栈深度大于虚拟机所允许的深度**，将抛出`StackOverflowError`异常
2. 如果虚拟机栈可以动态扩展，但是扩展时**无法申请到足够**内存，会抛`OutOfMemoryError`异常

### 注意
- 线程私有

## 3. 本地方法栈（Native Method Stack）

与虚拟机栈所发挥的作用相同，区别是虚拟机栈为虚拟机执行**Java方法**服务，而本地方法栈为虚拟机执行**Native方法**服务。
Java虚拟机规范并未强制规定其具体实现，如**HotSpot**虚拟机把虚拟机栈和本地方法栈**合二为一**。

### 注意
- 线程私有
- 本地方法栈和虚拟机栈一样会抛出`StackOverflowError`异常和`OutOfMemoryError`异常。

## 4. 堆（Java Heap）
           
### 定义
- 在虚拟机启动时创建，此内存区域的**唯一目的就是存放对象实例**
- **几乎所有**的对象实例都在这里分配内存（不绝对）

### 具体细节
- 堆是Java虚拟机所管理的内存中最大的一块，也是被**所有线程共享**的一块内存区域。
- Java堆是**垃圾收集器管理的主要区域**，因此很多时候也被称为“GC”堆（Garbage Collected Heap）。
- 现在内存回收基本都采用分代收集算法
  - 所以Java Heap细分为：新生代和老年代
  - 再细分新生代可分为：Eden、From Survivor、To Survivor，比例为8：1：1

### 异常
如果在堆中**没有内存完成实例分配，并且堆无法扩展时**，将会抛出`OutOfMemoryError`异常。

### 注意
- 所有线程共享
- Java堆可以像磁盘空间一样，允许逻辑上连续而物理不连续
- Java堆在实现上可时，可以实现成固定大小的，也可以按照可扩展实现（-Xmx和-Xms控制）

### 堆与分代垃圾回收

![堆](http://upload-images.jianshu.io/upload_images/11861611-ef8633f78265ef3a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

JDK8中把存放元数据中的永久内存从堆内存中移到了本地内存(native memory)中，JDK8中JVM堆内存结构就变成了如下：

![JDK8中JVM堆内存](http://upload-images.jianshu.io/upload_images/11861611-cfa3773a40258c1f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这样永久内存就不再占用堆内存，它可以通过自动增长来避免JDK7以及前期版本中常见的永久内存错误(java.lang.OutOfMemoryError: PermGen)。当然JDK8也提供了一个新的设置Matespace内存大小的参数，通过这个参数可以设置Matespace内存大小，这样我们可以根据自己项目的实际情况，避免过度浪费本地内存，达到有效利用。

```
-XX:MaxMetaspaceSize=128m //设置最大的元内存空间128兆
```

## 5. 方法区（Method Area）

### 定义
- 用于存储**已经被虚拟机加载**的**类信息、常量、静态变量、即时编译后的代码**等数据。
- Non-Heap：在Java虚拟机规范中，将方法区作为堆的一个逻辑部分来对待，但事实上，方法区并不是堆（Non-Heap）

### 具体细节
永久代：JavaGC的分代收集机制分为3个代：年青代，老年代，永久代，将方法区定义为“永久代”，这是因为，对于之前的HotSpot Java虚拟机的实现方式中，将分代收集的思想扩展到了方法区，并将方法区设计成了永久代。不过，除HotSpot之外的多数虚拟机，并不将方法区当做永久代，随着Java8的到来，已放弃永久代改为采用Native Memory来实现方法区的规划。


### 异常
- 方法区可选择不进行垃圾收集，垃圾收集在该区域也较少出现，但并非数据进入了方法区就不会被回收，该区域回收主要针对常量池回收和类型的卸载。
- 当方法区无法满足内存分配是将抛出`OutOfMemoryError`异常。

### 注意
- 所有线程共享
- 方法区在物理上也不需要是连续的，可以选择固定大小或可扩展大小
- 可以选择是否执行垃圾回收


## 6. 运行时常量池（Runtime Constant Pool）

### 定义
JDK1.7之前运行时常量池是方法区的一部分，JDK1.7及之后版本已经将运行时常量池从方法区中移了出来，在**堆（Heap）中**开辟了一块区域存放运行时常量池。

### 具体细节
Class文件中除了有类的版本、字段、方法、接口等信息外，还有一项信息是常量池（Constant Pool Tbale），用于存放编译期生成的各种**字面量和符号引用**，这部分内容在类加载后，进入运行时常量池中存放。
运行时常量池相对于Class文件常量池的另外一个重要特征是**具备动态性**，Java语言并不要求常量一定**只有编译期才能产生**，也是就并非预置入Class文件中的常量池的内容才能进入方法区运行时常量池，**运行期间也可能将新的常量放入池中**，这种被开发人员利用得多的便是**String类的intern()方法**

### 异常
会抛出`OutOfMemoryError`

## 7. HotSpot 方法区变迁

### 1、JDK1.2 ~ JDK6
在 JDK1.2 ~ JDK6 的实现中，HotSpot 使用永久代实现方法区；HotSpot 使用 GC 分代实现方法区带来了很大便利

### 2、JDK7
由于 GC 分代技术的影响，使之许多优秀的内存调试工具无法在 Oracle HotSpot之上运行，必须单独处理；并且 Oracle 同时收购了 BEA 和 Sun 公司，同时拥有 JRockit 和 HotSpot，在将 JRockit 许多优秀特性移植到 HotSpot 时由于 GC 分代技术遇到了种种困难，**所以从 JDK7 开始 Oracle HotSpot 开始移除永久代。**

**JDK7中符号表被移动到 Native Heap中，字符串常量和类引用被移动到 Java Heap中。**

### 3、JDK8
**在 JDK8 中，永久代已完全被元空间(Meatspace)所取代。**


参考文章
[百度百科的JVM定义](https://baike.baidu.com/item/JVM)
周志明，深入理解Java虚拟机：JVM高级特性与最佳实践，机械工业出版社
[《深入理解Java虚拟机》读书笔记](http://geosmart.github.io/2016/02/22/%E3%80%8A%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3Java%E8%99%9A%E6%8B%9F%E6%9C%BA%E3%80%8B%E8%AF%BB%E4%B9%A6%E7%AC%94%E8%AE%B0/)
[Java跨平台性](https://blog.csdn.net/u010046451/article/details/79121397)
[Java 是编译型语言还是解释型语言？](https://www.zhihu.com/question/19608553)
[HotSpot 方法区变迁](https://mritd.me/2016/03/22/Java-%E5%86%85%E5%AD%98%E4%B9%8B%E6%96%B9%E6%B3%95%E5%8C%BA%E5%92%8C%E8%BF%90%E8%A1%8C%E6%97%B6%E5%B8%B8%E9%87%8F%E6%B1%A0/)

