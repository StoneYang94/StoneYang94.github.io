---
title: Java I/O五.基础总结
date: 2018-08-08 20:13:02
tags: Java I/O
categories: Java I/O
---
本文对Java I/O基础知识复习的一个总结。

<!-- more -->

# 一、概览
Java 的 I/O 大概可以分成以下几类：

磁盘操作：File
字节操作：InputStream 和 OutputStream
字符操作：Reader 和 Writer
对象操作：Serializable
网络操作：Socket
新的输入/输出：NIO

# 二、磁盘操作
File 类可以用于表示文件和目录，但是它只用于表示`文件的信息`，而不表示文件的内容。

递归地输出一个目录下所有文件：

```java
public static void listAllFiles(File dir) {
    if (dir == null || !dir.exists()) {//base case
        return;
    }
    if (dir.isFile()) {//to do
        System.out.println(dir.getName());
        return;
    }
    for (File file : dir.listFiles()) {//reverse
        listAllFiles(file);
    }
}
```

# 三、字节操作(理解装饰者)

![image](http://upload-images.jianshu.io/upload_images/11861611-f623b872558363cd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Java I/O 使用了装饰者模式来实现。
- `InputStream` 是`抽象组件`
- `FileInputStream` 是 InputStream 的子类，属于`具体组件`，提供了字节流的输入操作
- `FilterInputStream` 属于`抽象装饰者`，装饰者用于装饰组件，为组件提供额外的功能，例如 BufferedInputStream 为 FileInputStream 提供缓存的功能。

## 装饰
实例化一个具有缓存功能的字节流对象时，只需要在 FileInputStream 对象上**再套一层** BufferedInputStream 对象即可。

```java
BufferedInputStream bis = new BufferedInputStream(new FileInputStream(file));
```

DataInputStream 装饰者提供了对更多数据类型进行输入的操作，比如 int、double 等基本类型。

## 批量读入文件内容到字节数组

```java
byte[] buf = new byte[20*1024];
int bytes = 0;
// 最多读取 buf.length 个字节，返回的是实际读取的个数，返回 -1 的时候表示读到 eof，即文件尾
while((bytes = in.read(buf, 0 , buf.length)) != -1) {
    // ...
}
```

# 四、字符操作(理解编码)

不管是磁盘还是网络传输，**最小**的存储单元都是字节，而不是字符，所以 I/O 操作的都是字节而不是字符。
但是在程序中操作的**通常**是字符形式的数据，因此需要提供对字符进行操作的方法。

## 编码解码

### 定义
编码就是把字符转换为字节，而解码是把字节重新组合成字符。
如果编码和解码过程使用不同的编码方式那么就出现了乱码。

### 编码方式

|编码方式|说明|
|--|--|
|GBK |中文字符占 2 个字节，英文字符占 1 个字节；
|UTF-8|中文字符占 3 个字节，英文字符占 1 个字节；
|UTF-16be|中文字符和英文字符都占 2 个字节。

UTF-16be 中的 be 指的是 Big Endian，也就是大端。相应地也有 UTF-16le，le 指的是 Little Endian，也就是小端。

### Java char 使用双字节编码 UTF-16be
这不是指 Java 只支持这一种编码方式，而是说 **char 这种类型使用 UTF-16be 进行编码**。char 类型占 16 位，也就是两个字节，Java 使用这种双字节编码是为了让一个中文或者一个英文都能使用一个 char 来存储。

###  例子
String 可以看成一个字符序列，可以指定一个编码方式将它编码为字节序列，也可以指定一个编码方式将一个字节序列解码为 String。

```java
String str1 = "中文";
byte[] bytes = str1.getBytes("UTF-8");
String str2 = new String(bytes, "UTF-8");
System.out.println(str2);
```

### 实际应用
InputStreamReader 实现从文本文件的字节流解码成字符流；OutputStreamWriter 实现字符流编码成为文本文件的字节流。它们继承自 Reader 和 Writer。

# 五、对象操作(理解序列化)

## 定义
序列化就是将一个`对象`转换成`字节序列`，方便存储和传输。

序列化：ObjectOutputStream.writeObject()

反序列化：ObjectInputStream.readObject()

序列化的类需要实现 Serializable 接口，它只是一个标准，没有任何方法需要实现。

### transient
transient 关键字可以使一些属性不会被序列化。

**ArrayList 序列化和反序列化的实现** ：ArrayList 中存储数据的数组是用 transient 修饰的，因为这个数组是动态扩展的，并不是所有的空间都被使用，因此就不需要所有的内容都被序列化。通过重写序列化和反序列化方法，使得可以只序列化数组中有内容的那部分数据。

```java
private transient Object[] elementData;
```

# 六、网络操作

## Java 中的网络支持：

1.  InetAddress：用于表示网络上的硬件资源，即 IP 地址；
2.  URL：统一资源定位符，通过 URL 可以直接读取或者写入网络上的数据；
3.  Sockets：使用 TCP 协议实现网络通信；
4.  Datagram：使用 UDP 协议实现网络通信。

### InetAddress
没有公有构造函数，只能通过静态方法来创建实例。

```java
InetAddress.getByName(String host);
InetAddress.getByAddress(byte[] addr);
```

## URL

可以直接从 URL 中读取字节流数据

```java
URL url = new URL("http://www.baidu.com");
InputStream is = url.openStream();                           // 字节流
InputStreamReader isr = new InputStreamReader(is, "utf-8");  // 字符流
BufferedReader br = new BufferedReader(isr);
String line = br.readLine();
while (line != null) {
    System.out.println(line);
    line = br.readLine();
}
br.close();
isr.close();
is.close();
```

## Sockets

*   ServerSocket：服务器端类
*   Socket：客户端类
*   服务器和客户端通过 InputStream 和 OutputStream 进行输入输出。

![image](http://upload-images.jianshu.io/upload_images/11861611-98dccfe0c3f24d7a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## Datagram

*   DatagramPacket：数据包类
*   DatagramSocket：通信类

# 七、NIO

新的输入/输出 (NIO) 库是在 JDK 1.4 中引入的。NIO 弥补了原来的 I/O 的不足，它在标准 Java 代码中提供了高速的、面向块的 I/O。

## 流与块

I/O 与 NIO 最重要的区别是数据打包和传输的方式
- I/O 以流的方式处理数据
- NIO 以块的方式处理数据。

面向流的 I/O 一次处理一个字节数据，一个输入流产生一个字节数据，一个输出流消费一个字节数据。为流式数据创建过滤器非常容易，链接几个过滤器，以便每个过滤器只负责单个复杂处理机制的一部分，这样也是相对简单的。不利的一面是，面向流的 I/O 通常相当慢。

一个面向块的 I/O 系统以块的形式处理数据，一次处理数据块。按块处理数据比按流处理数据要快得多。但是面向块的 I/O 缺少一些面向流的 I/O 所具有的优雅性和简单性。

I/O 包和 NIO 已经很好地集成了，java.io.* 已经以 NIO 为基础重新实现了，所以现在它可以利用 NIO 的一些特性。例如，java.io.* 包中的一些类包含以块的形式读写数据的方法，这使得即使在面向流的系统中，处理速度也会更快。

## 通道与缓冲区

### 1. 通道

通道 `Channel` 是对原 I/O 包中的流的模拟，可以通过它读取和写入数据。

通道与流的不同之处在于，流只能在一个方向上移动，(一个流必须是 InputStream 或者 OutputStream 的子类)，而通道是双向的，可以用于读、写或者同时用于读写。

通道包括以下类型：

*   FileChannel：从文件中读写数据；
*   DatagramChannel：通过 UDP 读写网络中数据；
*   SocketChannel：通过 TCP 读写网络中数据；
*   ServerSocketChannel：可以监听新进来的 TCP 连接，对每一个新进来的连接都会创建一个 SocketChannel。

### 2. 缓冲区

发送给一个通道的所有对象都必须首先放到缓冲区中，同样地，从通道中读取的任何数据都要读到缓冲区中。也就是说，不会直接对通道进行读写数据，而是要先经过缓冲区。

缓冲区实质上是一个数组，但它不仅仅是一个数组。缓冲区提供了对数据的结构化访问，而且还可以跟踪系统的读/写进程。

缓冲区包括以下类型：

*   ByteBuffer
*   CharBuffer
*   ShortBuffer
*   IntBuffer
*   LongBuffer
*   FloatBuffer
*   DoubleBuffer

## 缓冲区状态变量

|状态变量|作用|
|--|--|
|capacity|最大容量|
| position|当前已经读写的字节数|
|limit|还可以读写的字节数|

状态变量的改变过程举例：

① 新建一个大小为 8 个字节的缓冲区，此时 position 为 0，而 limit = capacity = 9。capacity 变量不会改变，下面的讨论会忽略它。

![image](http://upload-images.jianshu.io/upload_images/11861611-770abc813e32069d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

② 从输入通道中读取 3 个字节数据写入缓冲区中，此时 position 移动设为 3，limit 保持不变。

![image](http://upload-images.jianshu.io/upload_images/11861611-90cbde2dfef0c3e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

③ 以下图例为已经从输入通道读取了 5 个字节数据写入缓冲区中。在将缓冲区的数据写到输出通道之前，需要先调用 flip() 方法，这个方法将 limit 设置为当前 position，并将 position 设置为 0。

![image](http://upload-images.jianshu.io/upload_images/11861611-1d51954a6fde430f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

④ 从缓冲区中取 4 个字节到输出缓冲中，此时 position 设为 4。

![image](http://upload-images.jianshu.io/upload_images/11861611-9645ae4dd90ed204.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

⑤ 最后需要调用 `clear()` 方法来清空缓冲区，此时 position 和 limit 都被设置为最初位置。

![image](http://upload-images.jianshu.io/upload_images/11861611-7da0f7eb4bbc42de.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 文件 NIO 实例

以下展示了使用 NIO 快速复制文件的实例：

```java
public static void fastCopy(String src, String dist) throws IOException {
    FileInputStream fin = new FileInputStream(src); //获得源文件的输入字节流
    FileChannel fcin = fin.getChannel();//获取输入字节流的文件通道

    FileOutputStream fout = new FileOutputStream(dist);//获取目标文件的输出字节流 
    FileChannel fcout = fout.getChannel();//获取输出字节流的通道

   //获取输入字节流的文件通道为缓冲区分配 1024 个字节 
    ByteBuffer buffer = ByteBuffer.allocateDirect(1024);

    while (true) {     
        int r = fcin.read(buffer); //从输入通道中读取数据到缓冲区中
        if (r == -1) {// read() 返回 -1 表示 EOF
            break;
        }
        buffer.flip();//切换读写
        fcout.write(buffer);//把缓冲区的内容写入输出文件中 
        buffer.clear();//清空缓冲区
    }
}
```



## 内存映射文件

内存映射文件 I/O 是一种读和写文件数据的方法，它可以比常规的基于流或者基于通道的 I/O 快得多。

只有文件中实际读取或者写入的部分才会映射到内存中。

现代操作系统一般会根据需要将文件的部分映射为内存的部分，从而实现文件系统。Java 内存映射机制只不过是在底层操作系统中可以采用这种机制时，提供了对该机制的访问。

向内存映射文件写入可能是危险的，仅只是改变数组的单个元素这样的简单操作，就可能会直接修改磁盘上的文件。修改数据与将数据保存到磁盘是没有分开的。

下面代码行将文件的前 1024 个字节映射到内存中，map() 方法返回一个 MappedByteBuffer，它是 ByteBuffer 的子类。因此，您可以像使用其他任何 ByteBuffer 一样使用新映射的缓冲区，操作系统会在需要时负责执行映射。

```java
MappedByteBuffer mbb = fc.map(FileChannel.MapMode.READ_WRITE, 0, 1024);
```

## 对比

NIO 与普通 I/O 的区别主要有以下两点：
- NIO 是非阻塞的。应当注意，FileChannel 不能切换到非阻塞模式，套接字 Channel 可以。
-  NIO 面向块，I/O 面向流。

参考文章
[Java I/O](https://www.bookstack.cn/read/Interview-Notebook/notes-Java%20IO.md)
[Java I/O](https://github.com/CyC2018/Interview-Notebook/blob/master/notes/Java%20IO.md)