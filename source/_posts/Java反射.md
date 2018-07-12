---
title: Java反射
date: 2018-05-02 23:19:33
tags: Java反射 
categories: 编程语言
---
Java反射机制是指在运行状态中，对于任意一个类，都能够**知道**这个类的所有属性和方法；对于任意一个对象，都能够**调用**它的任意一个方法和属性。这种动态获取的信息以及动态调用对象的方法的功能称为java语言的反射机制。 
用一句话总结就是反射可以实现在**运行时(Runtime)**知道任意一个类的属性和方法。

<!-- more -->

# 反射的定义
反射(Reflection)是Java 程序开发语言的特征之一，它允许运行中的 Java 程序获取自身的信息，并且可以操作类或对象的内部属性。
Oracle官方对反射的解释是
> Reflection enables Java code to discover information about the fields, methods and constructors of loaded classes, and to use reflected fields, methods, and constructors to operate on their underlying counterparts, within security restrictions.
The API accommodates applications that need access to either the public members of a target object (based on its runtime class) or the members declared by a given class. It also allows programs to suppress default reflective access control.

简而言之，通过反射，我们可以在运行时获得程序或程序集中每一个类型的成员和成员的信息。
程序中一般的对象的类型都是在编译期就确定下来的，而Java反射机制可以动态地创建对象并调用其属性，这样的对象的类型在编译期是未知的。所以我们可以通过反射机制直接创建对象，**即使这个对象的类型在编译期是未知的。**
反射的核心是JVM在运行时才动态加载类或调用方法/访问属性，它不需要事先（写代码的时候或编译期）知道运行对象是谁。

# 反射的重要性
当程序运行时，允许改变程序结构或变量类型，这种语言称为动态语言。我们认为Java并不是动态语言，但是它却有一个非常突出的动态相关机制即反射。
IT行业里这么说，没有反射也就没有框架，现有的框架都是以反射为基础。在实际项目开发中，用的最多的是框架，填的最多的是类，反射这一概念就是将框架和类揉在一起的调和剂。所以反射才是接触项目开发的敲门砖。

# 使用反射流程

## 包
`Class` 和` java.lang.reflect` 一起对反射提供了支持，java.lang.reflect 类库主要包含了以下**三个类**：

`Field `：可以使用 get() 和 set() 方法读取和修改 Field 对象关联的字段；
`Method `：可以使用 invoke() 方法调用与 Method 对象关联的方法；
`Constructor `：可以用 Constructor 创建新的对象。

## 流程(详细过程在下一章)
[官方API](http://www.oracle.com/technetwork/articles/java/javareflection-1536171.html)

## 1. 获取 Class 对象---`Class.forName(“要加载的类全名”)`
获取特定类的 **Class**类，即该类对应的字节码，共三种方法：
1.   类名.class
2.  对象名.getClass()
3.  **Class.forName(“要加载的类全名”)**

## 2. 造构方法---`getConstructor(Class<?>... parameterTypes)`
调用 Class 对象的 **getConstructor(Class<?>... parameterTypes)**获取造构方法对象

## 3. 新建对象---`newInstance(Object... initargs)`
调用是构造方法类 Constructor  的 ** newInstance(Object... initargs)** 方法新建对象

## 4. 获取方法对象---`getMethod(String name, Class<?>... parameterTypes)`
调用Class对象的 **getMethod(String name, Class<?>... parameterTypes)**获取方法对象

## 5. 调用对象上相应方法---`invoke(Object obj, Object... args)`
调用方法对象类Method 的 **invoke(Object obj, Object... args)** 方法，调用对象上相应方法

# 反射的原理和运用
本节内容整理自[慕课网教学](https://www.imooc.com/video/3733/0)

## 一. Class类 

### 类是不是对象?
在面向对象的世界里，一切皆是对象。而在java语言中，有两个东西特殊：
- static修饰的东西不是对象，但是它属于类
- 普通的数据类型不是对象，例如：int a = 5;它不是面向对象，但是它有其包装类 Integer 或者分装类来弥补了它。

除了以上两种不是面向对象，其余的**包括类也有它的面向对象**，类是 `java.lang.Class` 的**实例化对象**（注意**C**是大写）。

### 可以用`new`实例化Class对象吗?
对于普通的对象，我们一般都会这样创建和表示(实例化)：

``` java
TestReflection t1 = new TestReflection();
```

我们会下意识地这样实例化Class类的对象(错误)：

``` java
Class c =new Class();//错误
```

查看Class源码，原因是**Class类构造器是私有的**，只有JVM可以创建Class的对象，因此不可以像普通类一样new一个Class对象，我们无法在代码中显式地声明一个 Class 对象。

>  Only the Java Virtual Machine creates Class objects.

``` java
    private Class(ClassLoader loader) {
        classLoader = loader;
    }
```

### 如何实例化Class对象
虽然我们不能用构造器new一个Class对象，但是却可以通过已有的类得到一个Class的 **实例对象**，共有三种方式，如下：

#### 1. 静态成员变量class---`类名.class`
任何一个类都有一个  **隐含的静态成员变量class**

``` java
Class c1 = TestReflection.class;
```

#### 2. 类的对象的getClass()---`对象.getClass()`

`t1`是`TestReflection`的一个对象，这种方式是通过一个类的对象的getClass()方法获得的

```java
Class c2 = t1.getClass();
```
#### 3. 类的全限定名---`Class.forName("包名+类名");`

```java
Class c3 = Class.forName("com.test.reflect.TestReflection");
```

#### 三种方式得到的一样吗

``` java
System.out.println(c1 == c2)? or System.out.println(c1 == c3)?
```

答案是肯定的，返回值为ture。这表明不论c1 or c2 or c3都代表了F类的类类型，也就是说一个类只可能是Class类的一个实例对象。
c1、c2、c3都是Class的对象，叫做**Code这个类的类类型（Class Type）**。
***类类型是反射的基础。***

### 通过类类型获取该类的对象
已经得到了类类型，可以通过类类型的`newInstance()`创建某个类的对象实例以及调用方法，如下：

``` java
TestReflection t1= (TestReflection)c1.newInstance(); // 必须要有无参的构造器
```

## 二. 动态加载
 程序执行分为编译器和运行期，编译时刻加载一个类就称为静态加载类，运行时刻加载类称为动态加载类。为了更好理解动态加载类和静态加载类的区别，抛开IDE工具，用记事本手写类,方便我们利用cmd命令行手动编译和运行一个类。

```java
class Office{
    public static void main(String[] args){
        if ("Word".equals(args[0])){   
            // 静态加载类，在编译时加载
            Word w = new Word();
            w.start();
        }
        if ("Excel".equals(args[0])){
            Excel e = new Excel();
            e.start();
        }
    }
}
```

使用`javac`命令编译Office.java

![静态加载](http://upload-images.jianshu.io/upload_images/11861611-66c2212619def74a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

由于我们`new`的两个类Word和Excel没有编译，所以报错了，这就是静态加载类的缺点，即必须在编译时期就加载所有可能用到的类，而我们希望实现的是运行时用到哪个类就加载哪个类，下面通过动态加载类来加以改进。

改进以后的类：OfficeBetter.java

```java
class OfficeBetter{   
    public static void main(String[] args){
        try{
            // 动态加载类，在运行时加载
            Class c = Class.forName(args[0]);
            // 通过类类型，创建该类对象
            OfficeAble oa = (OfficeAble)c.newInstance();
            oa.start();
        }catch (Exception e) {
            e.printStackTrace();
        }
    }   
}
```

这里**动态加载了名为`args[0]`的类**，而args[0]是在运行期输入给main方法的第一个参数，如果你输入Word那么就会加载Word.java，这时候就需要在与OfficeBetter.java相同路径下面创建Word.java；同理，如果你输入Excel就需要加载Excel.java了。 
其中OfficeAble是一个接口，上面动态加载的类如Word、Excel就是实现了OfficeAble，体现了多态的思想，这种动态加载和多态的思想可以使具体功能和代码解耦，也就是随时想添加某个功能（如Word和Excel都不要了，我要PPT）都能动态添加，而不改动原来的代码。**满足对修改封闭对扩展开放的思想，方便日后热插拔。**

其中OfficeAble接口如下：

```java
interface OfficeAble{
    public void start();
}
```

word类：

```java
class Word implements OfficeAble{
    public void start(){
        System.out.println("word...starts...");
    }
}
```

按顺序编译、运行上面的类：

![动态加载](http://upload-images.jianshu.io/upload_images/11861611-a2363588cf851230.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

总之**反射机制的优点就是可以实现动态创建对象和编译**，体现出很大的灵活性，特别是在J2EE的开发中 它的灵活性就表现的十分明显。比如，一个大型的软件，不可能一次就把把它设计的很完美，当这个程序编译后，发布了，当发现需要更新某些功能时，我们不可能要用户把以前的卸载，再重新安装新的版本，假如这样的话，这个软件肯定是没有多少人用的。采用静态的话，需要把整个程序重新编译一次才可以实现功能 的更新，而采用反射机制的话，它就可以不用卸载，只需要在运行时才动态的创建和编译，就可以实现该功能。 

## 三. 获取类的信息

一句话，类中有什么信息，它就可以获得什么信息，不过前提是得**知道类的名字**，否则巧妇难为无米之炊。
我们都知道一个类包括属性，普通方法和构造方法，这一节我们就学习如何通过类类型得到类的基本信息。

### 获取类的成员方法信息---`java.lang.reflect.Method`
在Java中，类的成员方法也是一个对象，它是java.lang.reflect.Method的一个对象，所以我们通过`java.lang.reflect.Method`里面封装的方法来获取这些信息。

#### 获取
单独获取某一个方法是通过`Class类`的以下方法获得的：

|方法|说明|
|--|--|
|public Method getDeclaredMethod(String name, Class<?>... parameterTypes) | 得到该类所有的方法，不包括父类的
|public Method getMethod(String name, Class<?>... parameterTypes) |得到该类所有的public方法，包括父类的

**eg.**

两个参数分别是**方法名**和方法参数类的**类类型**列表。 
例如`类 A`有`print(String a, int b)`方法：

```java
public void print(String a, int b) {
    // code body
}
```

现在知道A有一个`对象a`，那么就可以通过以下方式获取`print(String a, int b)`方法：

1. 获取类类型
2. 类类型.getDeclaredMethod()

```java
Class c = a.getClass();
Method method = c.getDeclaredMethod("print", String.class, int.class);
```

#### 调用
得到方法以后通过`Method类`的以下方法调用该方法：

```java
public Object invoke(Object obj, Object… args)
```

**eg.**
两个参数分别是这个`方法所属的对象`和这个`方法需要的参数`，还是用上面的例子来说明，通过：

```java
method.invoke(a, "hello", 10);
```

和通过普通调用效果完全一样，这就是方法的反射，invoke()方法可以反过来将其对象作为参数来调用方法，完全跟正常情况反了过来：

```java
a.print("hello", 10);
```

### 获取类的成员变量信息---`java.lang.reflect.Field`
类的成员变量也是一个对象，它是`java.lang.reflect.Field`的一个对象，所以我们通过java.lang.reflect.Field里面封装的方法来获取这些信息。

#### 获取
通过`Class类`的以下方法实现：

|方法|说明|
|--|--|
|public Field getDeclaredField(String name) |获得该类自身声明的所有变量，不包括其父类的变量
|public Field getField(String name) | 获得该类自所有的public成员变量，包括其父类变量

参数是成员变量的名字。 

**eg**
一个`类A`有成员变量`n`：

```java
private int n;
```

如果A有一个`对象a`，那么就可以这样得到其成员变量：

1. 获取类类型
2. 类类型.getDeclaredField()

```java
Class c = a.getClass();
Field field = c.getDeclaredField("n");
```

### 获取类的构造函数---`java.lang.reflect.Constructor`
同上，类的成构造函数也是一个对象，它是`java.lang.reflect.Constructor`的一个对象，所以我们通过**java.lang.reflect.Constructor**里面封装的方法来获取这些信息。

#### 获取
通过`Class类`的以下方法实现：

|方法|说明|
|--|--|
|public Constructor<T> getDeclaredConstructor(Class<?>... parameterTypes) |获得该类所以public构造器，包括父类
|public Constructor<T> getConstructor(Class<?>... parameterTypes) | 获得该类所以public构造器，包括父类

这个参数为构造函数参数类的类类型列表。 

**eg**
`类A`有一个构造函数`A(String a, int b)`：

```java
public A(String a, int b) {
    // code body
}
```

如果A有一个`对象a`，那么就可以通过以下的方式来获取这个构造函数：

```java
Constructor constructor = a.getDeclaredConstructor(String.class, int.class);
```

# 五.  通过反射了解集合泛型的本质

> Java中集合的泛型，是防止错误输入的，只在编译阶段有效，绕过编译到了运行期就无效了。即Java编程思想里说的泛型的“擦除”。

## 步骤
1. 首先观察**正常添加元素方式**，在编译器检查泛型，这个时候如果list2添加int类型会报错
2. 然后通过**反射添加元素方式**，在运行期动态加载类，首先得到list1和list2的类类型相同
3. 验证：再通过方法反射绕过编译器来调用add方法，看能否插入int型的元素

```java
import java.lang.reflect.Method;
import java.util.ArrayList;
import java.util.List;

public class GenericEssence {
    public static void main(String[] args) {
        List list1 = new ArrayList(); // 没有泛型 
        List<String> list2 = new ArrayList<String>(); // 有泛型
    //1---------------------------------------------------
        list2.add("hello");
        //list2.add(1); // list2有泛型限制，只能添加String，添加int报错
        System.out.println("list2的长度是：" + list2.size()); // 1

   //2---------------------------------------------------
        Class c1 = list1.getClass();
        Class c2 = list2.getClass();
        System.out.println(c1 == c2); // 结果：true，说明类类型完全相同
    //3---------------------------------------------------
        try {
            Method m = c2.getMethod("add", Object.class); // 方法反射得到add()
            m.invoke(list2, 20); // 给list2添加一个int型的，上面显示在编译器是会报错的
            System.out.println("list2的长度是：" + list2.size()); // 结果：2，说明list2长度增加了，并没有泛型检查
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
} 
```

![输出结果](http://upload-images.jianshu.io/upload_images/11861611-eeceebe6d869e70b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看出
- 在编译器的时候，泛型会限制集合内元素类型保持一致
- 但是编译器结束进入运行期以后，泛型就不再起作用了，即使是不同类型的元素也可以插入集合

参考文章
[浅谈 Java 中的 Class 类](https://blog.csdn.net/my_truelove/article/details/51289217)
[谈谈Java反射机制](https://www.jianshu.com/p/6277c1f9f48d)
[深入解析Java反射（1） - 基础](https://www.sczyh30.com/posts/Java/java-reflection-1/)
[java基础巩固笔记(1)-反射](https://brianway.github.io/2016/01/08/javase-learn-note-1-Reflect/)
[Java反射入门](https://blog.csdn.net/trigl/article/details/51042403)
[Using Java Reflection](http://www.oracle.com/technetwork/articles/java/javareflection-1536171.html)
[慕课网教学](https://www.imooc.com/video/3733/0)
[Java反射机制（源码反射优势解析）](https://blog.csdn.net/changlei_shennan/article/details/60868962)