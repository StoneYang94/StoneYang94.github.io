---
title: JVM六.JVM 参数
date: 2018-08-08 19:54:16
tags: JVM
categories: JVM
---
博主最近复习深入理解JVM一书，整理归纳，以形成系统认识和方便日后复习。
本文主要介绍
1. 参数规则
2. JVM常用参数

<!-- more -->

# 一些规则

## java启动参数共分为三类：
### 标准参数（-）
- 所有的JVM实现都必须实现这些参数的功能，而且向后兼容
- java -help检索出所有的标准参数
### 非标准参数（-X）
- 默认jvm实现这些参数的功能，但是并不保证所有jvm实现都满足，且不保证向后兼容
- 可以使用java -X检索 

![非标准参数](http://upload-images.jianshu.io/upload_images/11861611-5a0c414e05699f88.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 非Stable参数（-XX）
非标准 很长一段时间不会列出来 用于JVM开发的debug和调优

# 参数设置

## 堆内存 


![堆](http://upload-images.jianshu.io/upload_images/11861611-ef8633f78265ef3a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

JDK8中把存放元数据中的永久内存从堆内存中移到了本地内存(native memory)中，JDK8中JVM堆内存结构就变成了如下：

![JDK8中JVM堆内存](http://p7vxw6hv7.bkt.clouddn.com/18-8-8/67189708.jpg)


|参数名称	|含义|备注|
|--|--|--|
| -Xms|初始堆大小	|默认物理内存的1/64(<1GB)	默认(MinHeapFreeRatio参数可以调整)空余堆内存小于40%时，JVM就会增大堆直到-Xmx的最大限制；此值可以设置与-Xmx相同，以避免每次垃圾回收完成后JVM重新分配内存。
|-Xmx|最大堆大小|默认物理内存的1/4(<1GB)	默认(MaxHeapFreeRatio参数可以调整)空余堆内存大于70%时，JVM会减少堆直到 -Xms的最小限制
|-Xmn	|年轻代大小	|（eden+ 2 survivor space).增大年轻代后,将会减小年老代大小.此值对系统性能影响较大,Sun官方推荐配置为整个堆的3/8| 	 
|-XX:PermSize	|设置持久代(perm gen)初始值	|默认物理内存的1/64| 
|-XX:MaxPermSize	|设置持久代最大值|	默认物理内存的1/4	 |
|-Xss	|每个线程的堆栈大小	| 	JDK5.0以后每个线程堆栈大小为1M,以前每个线程堆栈大小为256K.更具应用的线程所需内存大小进行 调整.在相同物理内存下,减小这个值能生成更多的线程.但是操作系统对一个进程内的线程数还是有限制的,不能无限生成,经验值在3000~5000左右。一般小的应用， 如果栈不是很深， 应该是128k够用的 大的应用建议使用256k。这个选项对性能影响比较大，需要严格的测试。（校长）
|-XX:NewRatio|	年轻代与年老代的比值(除去持久代)	| 	-XX:NewRatio=4表示年轻代与年老代所占比值为1:4,年轻代占整个堆栈的1/5。
|-XX:SurvivorRatio	|Eden区与Survivor区的大小比值 |设置为8,则两个Survivor区与一个Eden区的比值为2:8,一个Survivor区占整个年轻代的1/10（默认8）
|-XX:MaxTenuringThreshold	|垃圾最大年龄	 |	如果设置为0的话,则年轻代对象不经过Survivor区,直接进入年老代. 对于年老代比较多的应用,可以提高效率.如果将此值设置为一个较大值,则年轻代对象会在Survivor区进行多次复制,这样可以增加对象再年轻代的存活 时间,增加在年轻代即被回收的概率



## 垃圾回收设置

| 配置 | 描述 |
| --- | --- |
| -XX:+UseSerialGC | 串行垃圾回收器 |
| -XX:+UseParallelGC | 并行垃圾回收器 |
| -XX:+UseConcMarkSweepGC | 并发标记扫描垃圾回收器 |
| -XX:ParallelCMSThreads= | 并发标记扫描垃圾回收器 = 为使用的线程数量 |
| -XX:+UseG1GC | G1 垃圾回收器 |

参考文章
[JVM参数汇总](https://www.cnblogs.com/duanxz/p/3482366.html)
[JVM常用参数设置](https://www.cnblogs.com/mazj611/p/3482429.html)
