---
title: Java I/O三.InputStream常用方法
date: 2018-06-24 23:50:49
tags: Java I/O
categories: Java I/O
---
本文主要介绍
1. InputStream常用方法罗列
2. InputStream类部分源码

<!-- more -->

- InputStream 为字节输入流，它本身为一个抽象类，必须依靠其子类实现各种功能，此抽象类是表示字节输入流的所有类的超类
-  继承自InputStream 的流都是向程序中输入数据的，且数据单位为字节（8bit）

# 装饰者模式
首先是输入流的最高超类-InputStream，该类也可以算是装饰者模式的最高级超类(装饰者模式-装饰者组合被装饰者类，调用被装饰者类的方法实现具体功能，装饰者则对其实现适当增加功能)。

然后有一个FilterInputStream类，继承InputStream，则可算是装饰者模式的装饰者超类。

此外，io包将流分为了节点流和处理流，节点流是FileInputStream、ByteArrayInputStream这些直接从某个地方获取流的类；处理流则是BufferedInputStream这种可以装饰节点流，来实现特定功能的类。

因此，节点流可以理解为装饰者模式中的被装饰者，处理流则是装饰者。

# 几种不同的InputStream
1.  FileInputStream
把一个**文件**作为InputStream，实现对文件的读取操作
2.  ByteArrayInputStream
把内存中的一个**缓冲区**作为InputStream使用
3.  StringBufferInputStream
把一个**String对象**作为InputStream
4.  PipedInputStream
实现了pipe的概念，主要在线程中使用
5.  SequenceInputStream
把多个InputStream合并为一个InputStream

- 输入流的最高超类-InputStream，是装饰者模式的最高级超类
- FilterInputStream类，继承InputStream，可算是装饰者模式的装饰者超类
- io包将流分为了节点流和处理流
  - 节点流是FileInputStream、ByteArrayInputStream这些直接从某个地方获取流的类
  - 处理流则是BufferedInputStream这种可以装饰节点流，来实现特定功能的类
  - 因此，节点流可以理解为装饰者模式中的被装饰者，处理流则是装饰者

# InputStream源码

## 常用方法

|方法|作用|一些返回值
|--|--|--|
|public abstract int `read( )`|读取一个byte的数据|若返回值=-1说明没有读取到任何字节读取工作结束
|public int `read(byte b[ ])`|读取b.length个字节的数据放到b数组中。该方法实际上是调用下一个方法实现的|返回值是读取的字节数
|public int `read(byte b[ ], int off, int len)`|从输入流中最多读取len个字节的数据，存放到偏移量为off的b数组中|
|public int available( )|返回输入流中可以读取的字节数。注意：若输入阻塞，当前线程将被挂起，如果InputStream对象调用这个方法的话，它只会返回0，这个方法必须由继承InputStream类的子类对象调用才有用
|public long skip(long n)|忽略输入流中的n个字节|返回值是实际忽略的字节数
|public int close( ) |使用完后，必须对我们打开的流进行关闭。|

## 定义

```java
package io;
import java.io.Closeable;
import java.io.IOException;

//输入流超类-装饰者模式超类 ,实现可关闭接口
public abstract class InputStream implements Closeable {
```

## 读取数据---`read( )`

### read( )

抽象方法，没有具体实现，因为子类必须实现此方法的一个实现。

- 用法

> Reads the next byte of data from the input stream. 

- 返回值

>The value byte is returned as an int in the range 0 to 255.
 If no byte is available because the end of the stream has been reached, the value -1 is returned.

- 代码

```java
public abstract int read() throws IOException
```

### read(byte b[ ])

从输入流中读取数据的一定数量字节，并存储在缓存字节数组b。

- 用法

> Reads some number of bytes from the input stream and stores them into the buffer array b.

- 返回值

> the total number of bytes read into the buffer, 
or -1 if there is no more data because the end of the stream has been reached.

- 代码

```java
public int read(byte b[]) throws IOException {
    return read(b, 0, b.length);
}
```

### read(byte b[ ], int off, int len)

在输入数据可用、检测到流末尾或者抛出异常前，此方法一直阻塞。

- 用法
让b[]偏移off个字节读取len长度的字节到b[]数组

> Reads up to len bytes of data from the input stream into an array of bytes. An attempt is made to read as many as len bytes, but a smaller number may be read. The number of bytes actually read is returned as an integer.

- 顺序
将读取的第一个字节存储在元素 b[off] 中，下一个存储在 b[off+1] 中
- 取出后偏移
此处的偏移并非要读取的in中的偏移，而是读取出字节后，从b[]数组的那个位置开始放起，也就是让b[]数组偏移off个字节

- 代码

```java
 public int read(byte b[], int off, int len) throws IOException {
        if (b == null) {
            throw new NullPointerException();
        } else if (off < 0 || len < 0 || len > b.length - off) {
            throw new IndexOutOfBoundsException();
        } else if (len == 0) {
            return 0;
        }
        int c = read(); //c表示读取到的字节-------------
        //读取到字节数为-1，表示没有可读字节
        if (c == -1) {
            return -1;//则返回-1：告知调用者，没有可读字节
        }
        //将读取到的字节从b[]数组偏移的下标处开始存入
        b[off] = (byte)c;
        int i = 1;  ////因为上面调用了一次read(),所以设置i为1，表示读取了1个字节-------
        try {
            //循环读取，直到 i(读取了的字节) 不小于 len(要读取的字节)
            for (; i < len ; i++) {
                c = read();//读取下一个字节
                if (c == -1) { //没读取到，跳出循环
                    break;
                }
                b[off + i] = (byte)c; //读取到了，就转为字节，然后存入b[]数组
            }
        } catch (IOException ee) {
        }
        return i;//返回读取到了的字节数
}
```

参数

> b - the buffer into which the data is read.
   off - the start offset in array b at which the data is written.
   len - the maximum number of bytes to read.



返回值

> the total number of bytes read into the buffer, or -1 if there is no more data because the end of the stream has been reached.

### 流结束的判断
方法read()的返回值为-1时；readLine()的返回值为null时。

## 其他

### skip(long n)
跳过n个字节,所谓的跳过也就是读取后抛弃

>  @param      n   the number of bytes to be skipped.
    @return     the actual number of bytes skipped.

```java
public long skip(long n) throws IOException {
    long remaining = n; //复制 总的跳过字节大小 到 剩余要跳过字节大小
    int nr;
    //如果要跳过的字节小于0，直接返回0，表示跳过了0个字节
    if (n <= 0) {
        return 0;
    }
    //从 要跳过的字节数 和 最大允许跳过的字节数 中取最小值
    int size = (int)Math.min(MAX_SKIP_BUFFER_SIZE, remaining);
    byte[] skipBuffer = new byte[size];//创建临时的要跳过字节大小的字节数组
    while (remaining > 0) {  // 剩余要跳过字节大小 > 0
             //A:如果 要跳过的字节数 < 最大允许跳过数
             //则直接读取 要 要跳过的字节数 大小的字节到临时数组
             // B:如果 要跳过的字节数 > 最大允许跳过数
             //则每次读取 最大允许跳过数 大小的字节到临时数组，循环，直到读取完成
        nr = read(skipBuffer, 0, (int)Math.min(size, remaining));
             //如果read()返回值小于0,表示没有可读字节，直接跳出循环
             // 该情况出现在，当要跳过的字节大小 大于 输入流剩余字节大小的时候
        if (nr < 0) {
            break;
        }
             //如果是A，则相减后==0，相当于只执行一次循环
             // 如果是B，则每次读取最大允许跳过数大小的字节，循环读取，直到读取/跳过到指定大小的字节数
        remaining -= nr;
    }  
        return n - remaining;//返回跳过了多少个字节数
}
```

```java
private static final int MAX_SKIP_BUFFER_SIZE = 2048;//最大可跳过缓冲数组大小
```

## available()

- 返回输入流中可以读取的字节数
- 若输入阻塞，当前线程将被挂起，如果InputStream对象调用这个方法的话，它只会返回0
- 这个方法必须由继承InputStream类的子类对象调用

```java
public int available() throws IOException {
    return 0;
}
```

## close()

我们在使用完后，必须对我们打开的流进行关闭

```java
public void close() throws IOException {}
```

参考文章
[官方文档](https://docs.oracle.com/javase/8/docs/api/)
[java回忆录—输入输出流详细讲解（入门经典）](https://blog.csdn.net/qq_22063697/article/details/52137369)
[深入分析 Java I/O 的工作机制](https://www.ibm.com/developerworks/cn/java/j-lo-javaio/index.html)
