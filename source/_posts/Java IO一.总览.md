---
title: Java I/O一.总览
date: 2018-06-18 23:52:28
tags: Java I/O
categories: Java I/O
---
本文主要Java 的 I/O最重要5个类、一个接口。

<!-- more -->

# 数据流

## 数据流是什么
数据流是一组有序，有起点和终点的字节的数据序列，包括输入流和输出流。

- 数据流是一串连续不断的数据的集合，就象水管里的水流，在水管的一端一点一点地供水，而在水管的另一端看到的是一股连续不断的水流。
- 写入
数据写入程序可以是一段、一段地向数据流管道中写入数据，这些数据段会按**先后顺序**形成一个长的数据流。
- 读取
对数据读取程序来说，看不到数据流在写入时的分段情况，每次可以读取其中的任意长度的数据，但**只能先读取前面的数据后，再读取后面的数据（不能随机读取）**。
- 效果
不管写入时是将数据分多次写入，还是作为一个整体一次写入，读取时的效果都是完全一样的。


## 数据流分类

### 按类型
流序列中的数据既可以是未经加工的原始二进制数据，也可以是经一定编码处理后符合某种格式规定的特定数据。
1. 字节流：数据流中最小的数据单元是字节
2. 字符流：数据流中最小的数据单元是字符， Java中的字符是Unicode编码，一个字符占用两个字节

### 按方向（站在程序的角度）
- 输入流
 程序从输入流读取数据源。数据源包括外界(键盘、文件、网络…)，即是将数据源读入到程序的通信通道。
- 输出流
 程序向输出流写入数据。将程序中的数据输出到外界（显示器、打印机、文件、网络…）的通信通道。

### 意义
- 采用数据流的目的就是使得输出输入独立于设备
Input Stream不关心数据源来自何种设备（键盘，文件，网络） 
Output Stream不关心数据的目的是何种设备（键盘，文件，网络）

# 存储
## 存储方式
在电脑上的数据有三种存储方式
外存：电脑上的硬盘，磁盘，U盘
内存：内存条
缓存：CPU

## 对比
存储量（依次递减）：    外存-->内存-->缓存
读取速度（依次递减）：  缓存-->内存-->外存

# Java 的 I/O 
- 主要是指使用Java进行输入，输出操作
- Java 的 I/O 操作类在包 java.io 下
- Java所有的I/O机制**都是基于数据流**进行输入输出，这些数据流表示了字符或者字节数据的流动序列

## 最重要5个类、一个接口
- 5个类指的是File、OutputStream、InputStream、Writer、Reader
- 一个接口指的是Serializable

1.  File（文件特征与管理）：用于文件或者目录的描述信息，例如生成新目录，修改文件名，删除文件，判断文件所在路径等。
2.  InputStream（字节流，二进制格式操作）：抽象类，基于字节的输入操作，是所有输入流的父类。定义了所有输入流都具有的共同特征。
3.  OutputStream（字节流，二进制格式操作）：抽象类。基于字节的输出操作。是所有输出流的父类。定义了所有输出流都具有的共同特征。
4.  Reader（字符流，文本格式操作）：抽象类，基于字符的输入操作。
5.  Writer（字符流，文本格式操作）：抽象类，基于字符的输出操作。
6.  RandomAccessFile（随机文件操作）：它的功能丰富，**可以从文件的任意位置进行存取（输入输出）操作**。

![I/O体系](http://upload-images.jianshu.io/upload_images/11861611-468a8ecb4fd207d4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

主要包括如下三个层次：
1. **流式**部分――IO的主体部分；
2. **非流式**部分――主要包含一些辅助流式部分的类，如：File类、RandomAccessFile类和FileDescriptor等类；
3. 其他类–文件**读取**部分的与**安全**相关的类，如：SerializablePermission类，以及与本地操作系统相关的文件系统的类，如：FileSystem类和Win32FileSystem类和WinNTFileSystem类。

# I/O流

![I/O流](http://upload-images.jianshu.io/upload_images/11861611-39e7b6547e8d18c3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

java I/O 的设计使用到了 Decorator(装饰器)模式，按功能划分Stream，您可以动态装配这些 Stream，以便获得您需要的功能。
如FilterInputStream和FilterOutputStream是抽象装饰角色，而其他派生自它们的类则是具体装饰角色。

# 常用方法
## Inputstream

|方法|作用|一些返回值
|--|--|--|
|public abstract int `read( )`|读取一个byte的数据|若返回值=-1说明没有读取到任何字节读取工作结束
|public int `read(byte b[ ])`|读取b.length个字节的数据放到b数组中。该方法实际上是调用下一个方法实现的|返回值是读取的字节数
|public int `read(byte b[ ], int off, int len)`|从输入流中最多读取len个字节的数据，存放到偏移量为off的b数组中|
|public int available( )|返回输入流中可以读取的字节数。注意：若输入阻塞，当前线程将被挂起，如果InputStream对象调用这个方法的话，它只会返回0，这个方法必须由继承InputStream类的子类对象调用才有用
|public long skip(long n)|忽略输入流中的n个字节|返回值是实际忽略的字节数
|public int close( ) |使用完后，必须对我们打开的流进行关闭。|


## OutputStream

|方法|作用|
|--|--|
|public void `write(byte b[ ])`|将参数b中的字节写到输出流|
|public void `write(byte b[ ], int off, int len)` |将参数b的从偏移量off开始的len个字节写到输出流|
|public abstract void `write(int b)` |先将int转换为byte类型，把低字节写入到输出流中|
|public void flush( )| 将数据缓冲区中数据全部输出，并清空缓冲区|
|public void close( ) |关闭输出流并释放与流相关的系统资源|


参考文章
[深入分析 Java I/O 的工作机制](https://www.ibm.com/developerworks/cn/java/j-lo-javaio/index.html)
[java回忆录—输入输出流详细讲解（入门经典）](https://blog.csdn.net/qq_22063697/article/details/52137369)
[官方文档](https://docs.oracle.com/javase/8/docs/api/)