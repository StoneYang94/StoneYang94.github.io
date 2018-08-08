---
title: Java I/O四.OutStream常用方法
date: 2018-08-08 20:11:59
tags: Java I/O
categories: Java I/O
---
本文主要介绍
1. OutStream常用方法罗列
2. OutStream类部分源码

<!-- more -->

OutputStream提供了3个write()来做数据的输出，这个是和InputStream的read()是相对应的

# 实现类

几种不同的OutputStream：
1.  ByteArrayOutputStream
把信息存入内存中的一个**缓冲区**中
2.  FileOutputStream
把信息存入**文件**中
3.  PipedOutputStream
实现了pipe的概念，主要在线程中使用
4.  SequenceOutputStream
把多个OutStream合并为一个OutStream

# 源码

## 常用方法

|方法|作用|
|--|--|
|public void `write(byte b[ ])`|将参数b中的字节写到输出流|
|public void `write(byte b[ ], int off, int len)` |将参数b的从偏移量off开始的len个字节写到输出流|
|public abstract void `write(int b)` |先将int转换为byte类型，把低字节写入到输出流中|
|public void flush( )| 将数据缓冲区中数据全部输出，并清空缓冲区|
|public void close( ) |关闭输出流并释放与流相关的系统资源|

## 定义

```java
package java.io;

public abstract class OutputStream implements Closeable, Flushable 
```

## 写数据

### write(int b)
- 先将int转换为byte类型，把低字节写入到输出流中。此方法丢弃 int 类型高位的 3 个字节，只保留低位的 1 个字节写入
- 抽象方法，没有具体实现，因为子类必须实现此方法的一个实现


- 用法

> Writes the specified byte to this output stream. The general contract for <code>write</code> is that one byte is written to the output stream. The byte to be written is the eight low-order bits of the argument <code>b</code>. The 24 high-order bits of <code>b</code> are ignored.<p>

- 代码

```java
public abstract void write(int b) throws IOException;
```

### write(byte b[])

将参数b中的字节写到输出流

- 用法

>  Writes <code>b.length</code> bytes from the specified byte array to this output stream.

- 代码

```java
public void write(byte b[]) throws IOException {
    write(b, 0, b.length);
}
```

### write(byte b[], int off, int len) 

将指定 byte 数组中从偏移量 off 开始的 len 个字节写入此输出流。

- 用法


> Writes <code>len</code> bytes from the specified byte array starting at offset <code>off</code> to this output stream.

- 代码

```java
public void write(byte b[], int off, int len) throws IOException {  
    if (b == null) {// 如果字节数组为空，抛出异常
        throw new NullPointerException();
    } else if ((off < 0) || (off > b.length) || (len < 0) ||
               ((off + len) > b.length) || ((off + len) < 0)) { // 下标及范围不合适
        throw new IndexOutOfBoundsException();
    } else if (len == 0) {// 写入长度为0，不需要处理
        return;
    }       
    for (int i = 0 ; i < len ; i++) { //从off下标处开始写入每一个字节到输出流
        write(b[off + i]);
    }
}
```

## flush()
> Flushes this output stream and forces any buffered output bytes to be written out.

- 将数据缓冲区中数据全部输出，并清空缓冲区
- 此类未实现具体行为，子类应该复写此方法

```java
public void flush() throws IOException {
}
```

## close()
> Closes this output stream and releases any system resources associated with this stream. 

- 关闭此输出流并释放与此流有关的所有系统资源
- 此类未实现具体行为，子类应该复写此方法

```java
public void close() throws IOException {
}
```

参考文章
[官方文档](https://docs.oracle.com/javase/8/docs/api/)
[java回忆录—输入输出流详细讲解（入门经典）](https://blog.csdn.net/qq_22063697/article/details/52137369)
[深入分析 Java I/O 的工作机制](https://www.ibm.com/developerworks/cn/java/j-lo-javaio/index.html)