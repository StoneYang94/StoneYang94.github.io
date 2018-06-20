---
title: File源码学习-基于JDK8
date: 2018-06-20 22:22:25
tags: 
- I/O
- 源码
categories: Java I/O
---
在Java语言的java.io包中，由File类提供了描述文件和目录的操作与管理方法。 

<!-- more -->

#  作用
File类主要用于命名文件、查询文件属性和处理文件目录。
File类不是InputStream、OutputStream或Reader、Writer的子类，因为它不负责数据的输入输出，而**专门用来管理磁盘文件与目录**。

# 源码

## 定义

```java
public class File implements Serializable, Comparable<File>
```

## 构造函数

File类共提供了四个不同的构造函数，以不同的参数形式灵活地接收文件和目录名信息。构造函数：

### File (String pathname)

> Creates a new <code>File</code> instance by converting the given pathname string into an abstract pathname.  

```java
    public File(String pathname) {
        if (pathname == null) {
            throw new NullPointerException();
        }
        this.path = fs.normalize(pathname);
        this.prefixLength = fs.prefixLength(this.path);
    }
```

 例:创建文件对象f1，f1所指的文件是在当前目录下创建的FileTest1.txt

```java
File  f1 = new File("FileTest1.txt"); 
```

### File（URI uri）
> Creates a new <tt>File</tt> instance by converting the given <tt>file:</tt> URI into an abstract pathname.

### File (String parent , String child)
>  Creates a new <code>File</code> instance from a parent pathname string and a child pathname string.

```java
public File(String parent, String child) {
    if (child == null) {
        throw new NullPointerException();
    }
    if (parent != null) {
        if (parent.equals("")) {
            this.path = fs.resolve(fs.getDefaultParent(),
                                   fs.normalize(child));
        } else {
            this.path = fs.resolve(fs.normalize(parent),
                                   fs.normalize(child));
        }
    } else {
        this.path = fs.normalize(child);
    }
    this.prefixLength = fs.prefixLength(this.path);
}
```

 例: D:\\dir1目录事先必须存在，否则异常

```
File f2 = new File(“D:\\dir1","FileTest2.txt");
```

### File (File parent , String child)

```java
public File(File parent, String child) {
    if (child == null) {
        throw new NullPointerException();
    }
    if (parent != null) {
        if (parent.path.equals("")) {
            this.path = fs.resolve(fs.getDefaultParent(),
                                   fs.normalize(child));
        } else {
            this.path = fs.resolve(parent.path,
                                   fs.normalize(child));
        }
    } else {
        this.path = fs.normalize(child);
    }
    this.prefixLength = fs.prefixLength(this.path);
}
```

 例:   
在如果 E：\\dir3目录不存在则需要先使用f4.mkdir()先创建

```java
File  f4 = new File("E：\\dir3");
File  f5 = new File(f4,"FileTest5.txt"); 
```

## 获得属性
一个对应于某磁盘文件或目录的File对象一经创建， 就可以通过调用它的方法来获得**文件或目录的属性**。
1. public boolean exists( ) 
判断文件或目录是否存在

1. public boolean isFile( ) 
判断是文件还是目录 

2. public boolean isDirectory( ) 
判断是文件还是目录

3. public String getName( ) 
返回文件名或目录名

4. public String getPath( ) 
返回文件或目录的路径

5. public long length( ) 
获取文件的长度 

6. public String[ ] list ( ) 
将目录中所有文件名和目录名保存在字符串数组中返回

7. public File[] listFiles() 
返回某个目录下所有文件和目录的绝对路径，返回的是File数组

9. public String getAbsolutePath() 
返回文件或目录的绝对路径

## 管理
   File类中还定义了一些对文件或目录进行管理、操作的方法，常用的方法有：
1. public boolean renameTo( File newFile )
  重命名文件

2. public void delete( )
  删除文件

3. public boolean mkdir( )
创建目录

4. public boolean createNewFile(
创建文件

## 总结：
1. 输入path 以'/' 或者 ’\\‘ 开头的 ，是以项目所在的硬盘位基础路径
2. 输入path 以 字母开头 的,是以项目的路径为基础路径 即： System.getProperty("user.dir")
3. 输入绝对路径的，就是以该绝对路径做为路径咯

# 一个例子

## 代码

```java
public class FileTest {
	public static void listDir(String dir) throws IOException {
		File file = new File(dir);
		if (!file.isDirectory()) {
			throw new IOException(dir + "  并不是目录");
		}
		File[] files = file.listFiles();
		for (File f : files) {
			if (f.isDirectory()) {// 多级目录，递归调用
				listDir(f.getAbsolutePath());
			} else {// 是文件就直接输出该文件的绝对路径
				System.out.println(f.getAbsolutePath());
			}
		}
	}
	
    public static void main(String[] args) throws IOException {
        FileTest.listDir("D:\\Java\\bin");
    }
}
```

## 输出

![输出](http://upload-images.jianshu.io/upload_images/11861611-746cc8cb6d595d47.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

参考文章
[java回忆录—输入输出流详细讲解（入门经典）](https://blog.csdn.net/qq_22063697/article/details/52137369)
[深入分析 Java I/O 的工作机制](https://www.ibm.com/developerworks/cn/java/j-lo-javaio/index.html)






