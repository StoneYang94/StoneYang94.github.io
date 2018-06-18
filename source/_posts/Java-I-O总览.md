---
title: Java I/O总览
date: 2018-06-18 23:52:28
tags: I/O
categories: Java I/O
---
# 定义

## 数据流

### 是什么
数据流是一组有序，有起点和终点的字节的数据序列，包括输入流和输出流。

- 数据流是一串连续不断的数据的集合，就象水管里的水流，在水管的一端一点一点地供水，而在水管的另一端看到的是一股连续不断的水流。
- 写入
数据写入程序可以是一段、一段地向数据流管道中写入数据，这些数据段会按**先后顺序**形成一个长的数据流。
- 读取
对数据读取程序来说，看不到数据流在写入时的分段情况，每次可以读取其中的任意长度的数据，但**只能先读取前面的数据后，再读取后面的数据（不能随机读取）**。
- 效果
不管写入时是将数据分多次写入，还是作为一个整体一次写入，读取时的效果都是完全一样的。

<!-- more -->

### 分类

#### 按类型
流序列中的数据既可以是未经加工的原始二进制数据，也可以是经一定编码处理后符合某种格式规定的特定数据。
1. 字节流：数据流中最小的数据单元是字节
2. 字符流：数据流中最小的数据单元是字符， Java中的字符是Unicode编码，一个字符占用两个字节

#### 按方向（站在程序的角度）
- 输入流
 程序从输入流读取数据源。数据源包括外界(键盘、文件、网络…)，即是将数据源读入到程序的通信通道。
- 输出流
 程序向输出流写入数据。将程序中的数据输出到外界（显示器、打印机、文件、网络…）的通信通道。

### 意义
Input Stream不关心数据源来自何种设备（键盘，文件，网络） 
Output Stream不关心数据的目的是何种设备（键盘，文件，网络）
- 采用数据流的目的就是使得输出输入独立于设备

## 存储

### 存储方式
在电脑上的数据有三种存储方式
外存：电脑上的硬盘，磁盘，U盘
内存：内存条
缓存：CPU

### 对比
存储量（依次递减）：    外存-->内存-->缓存
读取速度（依次递减）：  缓存-->内存-->外存

## I/O分析

###  I/O
I/O 问题是任何编程语言都无法回避的问题，可以说 I/O 问题是整个人机交互的核心问题，因为 I/O 是机器获取和交换信息的主要渠道。

### Java 的 I/O 
- 主要是指使用Java进行输入，输出操作
- Java 的 I/O 操作类在包 java.io 下
- Java所有的I/O机制**都是基于数据流**进行输入输出，这些数据流表示了字符或者字节数据的流动序列

### 最重要5个类、一个接口
- 5个类指的是File、OutputStream、InputStream、Writer、Reader
- 一个接口指的是Serializable

1.  File（文件特征与管理）：用于文件或者目录的描述信息，例如生成新目录，修改文件名，删除文件，判断文件所在路径等。
2.  InputStream（字节流，二进制格式操作）：抽象类，基于字节的输入操作，是所有输入流的父类。定义了所有输入流都具有的共同特征。
3.  OutputStream（字节流，二进制格式操作）：抽象类。基于字节的输出操作。是所有输出流的父类。定义了所有输出流都具有的共同特征。
4.  Reader（字符流，文本格式操作）：抽象类，基于字符的输入操作。
5.  Writer（字符流，文本格式操作）：抽象类，基于字符的输出操作。
6.  RandomAccessFile（随机文件操作）：它的功能丰富，**可以从文件的任意位置进行存取（输入输出）操作**。

![I/O体系](http://p7vxw6hv7.bkt.clouddn.com/18-6-15/32681708.jpg)

# I/O流

![I/O流](http://p7vxw6hv7.bkt.clouddn.com/18-6-15/91986983.jpg)


FilterInputStream和FilterOutputStream是抽象装饰角色，而其他派生自它们的类则是具体装饰角色。

按来源/去向分类

1.  File（文件）： FileInputStream, FileOutputStream, FileReader, FileWriter
2.  byte[]：ByteArrayInputStream, ByteArrayOutputStream
3.  Char[]: CharArrayReader, CharArrayWriter
4.  String: StringBufferInputStream, StringReader, StringWriter
5.  网络数据流：InputStream, OutputStream, Reader, Writer


# 如何选择I/O流
1.  确定是输入还是输出
    输入:输入流 InputStream Reader
    输出:输出流 OutputStream Writer
2.  明确操作的数据对象是否是纯文本
    是:字符流 Reader，Writer
    否:字节流 InputStream，OutputStream
3.  明确具体的设备。
- 文件：
        读：FileInputStream,, FileReader,
        写：FileOutputStream，FileWriter
- 数组：
        byte[ ]：ByteArrayInputStream, ByteArrayOutputStream
        char[ ]：CharArrayReader, CharArrayWriter
- String：
        StringBufferInputStream(已过时，因为其只能用于String的每个字符都是8位的字符串), StringReader, StringWriter
- Socket流
        键盘：用System.in（是一个InputStream对象）读取，用System.out（是一个OutoutStream对象）打印
4.  是否需要转换流
    是，就使用转换流，从Stream转化为Reader、Writer：InputStreamReader，OutputStreamWriter
5.  是否需要缓冲提高效率
    是就加上Buffered：BufferedInputStream, BufferedOuputStream, BufferedReader, BufferedWriter
6.  是否需要格式化输出

# 示例代码

*   将标准输入（键盘输入）显示到标准输出（显示器），支持字符。

```java
char ch;
BufferedReader in = new BufferedReader(new InputStreamReader(System.in)); 
try {
    while ((ch = (char) in.read()) != -1){
        System.out.print(ch);
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

*   将AtomicityTest.java的内容打印到显示器

方法一：

```java
BufferedReader in = new BufferedReader(new FileReader("AtomicityTest.java"));

String s;

try {

    while ((s = in.readLine()) != null){

        System.out.println(s);

    }

    in.close();

} catch (IOException e) {

    e.printStackTrace();

}
```


方法二：

```java
int b;

try {

    while ((b = in.read()) != -1){

        System.out.print((char)b);

    }

    in.close();

} catch (IOException e) {

    e.printStackTrace();

}
```


方法三：(有可能出现乱码）

```java
FileInputStream in = new FileInputStream("AtomicityTest.java");

int n = 50;

byte[] buffer = new byte[n];

try {

    while ((in.read(buffer,0,n) != -1 && n > 0)){

        System.out.print(new String(buffer));

    }

    in.close();

} catch (IOException e) {

    e.printStackTrace();

}
```

*   将文件A的内容拷贝到文件B

```java
FileInputStream in = new FileInputStream("AtomicityTest.java");

FileOutputStream out = new FileOutputStream("copy.txt");

int b;

while ((b = in.read()) != -1){

    out.write(b);

}

out.flush();

in.close();

out.close();

```

*   将标准输入的内容写入文件

```java
Scanner in = new Scanner(System.in);

FileWriter out = new FileWriter("systemIn.log");

String s;

while (!(s = in.nextLine()).equals("Q")){

    out.write(s + "\n");

}

out.flush();

out.close();

in.close();
```



参考文章
[深入分析 Java I/O 的工作机制](https://www.ibm.com/developerworks/cn/java/j-lo-javaio/index.html)
[java回忆录—输入输出流详细讲解（入门经典）](https://blog.csdn.net/qq_22063697/article/details/52137369)
[官方文档](https://docs.oracle.com/javase/8/docs/api/)