---
title: Java中的不可变类
date: 2018-08-20 23:39:51
tags: 
- 面试反思
- 编程语言
categories: 面试反思
---

# 总览

## 不可变类定义
不可变类`Immutable Objects`：当类的实例一经创建，其内容便不可改变，即无法修改其**成员变量**。

可变类`Mutable Objects`：类的实例创建后，可以修改其内容。

<!-- more -->

## 不可变类例子
Java 中**八个基本类型的包装类和 String 类都属于不可变类**，而其他的大多数类都属于可变类。

## 与引用不可变(final)的区别

分类|什么不可变|怎么“改变”
|--|--|--|
|不可变类|该类的实例不可变|新创建一个对象并将引用指向新对象来实现变化的
|引用不可变|指向该实例的引用不可变|该引用所指对象的内存地址是不可变的，但并非该对象不可变

## 不可变类分析

```
String s = "abc";
System.out.println("s:" + s);  // 输出s:abc
s = "xyz";
System.out.println("s:" + s);  // 输出s:xyz
```

以上代码显示，不可变类 String 貌似是可以改变值的，但实际上并不是。变量 s 只是一个指向 String 类的实例的引用，存储的是实例对象在内存中的地址。

代码中第三行的 “改变” 实际上是`新实例化`了一个 String 对象，并将 s 的`指向修改`到新对象上，而原来的对象在内存中并未发生变化，只是少了一个指向它的引用，并且在未来被垃圾回收前它都将保持不变。

# 如何创建不可变类

关于创建不可变对象，Oracle也有说明：[详细介绍](https://link.jianshu.com?t=http://docs.oracle.com/javase/tutorial/essential/concurrency/imstrat.html)
主要是以下几点：

> 1.  Don't provide "setter" methods — methods that modify fields or objects referred to by fields
> 2.  Make all fields final and private
> 3.  Don't allow subclasses to override methods. The simplest way to do this is to declare the class as final. A more sophisticated approach is to make the constructor private and construct instances in factory methods.
> 4.  If the instance fields include references to mutable objects, don't allow those objects to be changed:
> 
> *   Don't provide methods that modify the mutable objects.
> *   Don't share references to the mutable objects. Never store references to external, mutable objects passed to the constructor; if necessary, create copies, and store references to the copies. Similarly, create copies of your internal mutable objects when necessary to avoid returning the originals in your methods.

1.  不提供`setter`方法，避免对象的域被修改
2.  将所有的域都设置为`private final`
3.  不允许子类覆盖父类方法。最简单的方法是将`class设为final`。更好点的方式是将构造方法设为private，同时通过工厂方法来创建实例
4.  如果域包含其他可变类的对象，也要禁止这些对象被修改：
-  `不提供修改`可变对象的方法
- `不要共享`指向可变对象的`引用`。不要存储那些传进构造方法的外部可变对象的引用；如果需要，创建拷贝，保存指向拷贝的引用。类似的，在创建方法返回值时，避免返回原始的内部可变对象，而是返回可变对象的拷贝。

根据以上规则可以实现一个不可变类

```
import java.util.Date;

/**
* 注意实例的变量本身可能是不可变的，也可能是可变的
* 对于所有可变的成员变量，返回时需要复制一份新的
* 不可变的成员变量不用做特殊处理
* */
public final class ImmutableClass{

    /**
    * Integer类是不可变的，因为它没有提供任何setter方法来改变值
    * */
    private final Integer immutableField1;
    /**
    * String类是不可变的，它也没有提供任何setter方法来改变值
    * */
    private final String immutableField2;
    /**
    * Date类是可变的，它提供了改变日期或时间的setter方法
    * */
    private final Date mutableField;

    // 将构造方法声明为private，确保不会有意外情况构造这个类
    private ImmutableClass(Integer fld1, String fld2, Date date){
        this.immutableField1 = fld1;
        this.immutableField2 = fld2;
        this.mutableField = new Date(date.getTime());
    }

    // 工厂方法将创建对象的逻辑封装在一个地方
    public static ImmutableClass createNewInstance(Integer fld1, String fld2, Date date){
        return new ImmutableClass(fld1, fld2, date);
    }

    // 不提供setter方法

    /**
    * Integer类是不可变的，可以直接返回成员变量的实例
    * */
    public Integer getImmutableField1() {
        return immutableField1;
    }

    /**
    * String类是不可变的，可以直接返回成员变量的实例
    * */
    public String getImmutableField2() {
        return immutableField2;
    }

    /**
    * Date类是可变的，需要注意一下
    * 不要返回原始成员变量的引用
    * 创建一个新的Date对象，内容和成员变量一样
    * */
    public Date getMutableField() {
        return new Date(mutableField.getTime());
    }

    @Override
    public String toString() {
        return immutableField1 +" - "+ immutableField2 +" - "+ mutableField;
    }

    public static void main(String[] args){
        ImmutableClass im = ImmutableClass.createNewInstance(100,"test", new Date());
        System.out.println(im);
        tryModification(im.getImmutableField1(),im.getImmutableField2(),im.getMutableField());
        System.out.println(im);
    }

    private static void tryModification(Integer immutableField1, String immutableField2, Date mutableField){
        immutableField1 = 10000;
        immutableField2 = "test changed";
        mutableField.setDate(10);
    }
}
```


Output: (content is unchanged)

```
100 - test - Sun Aug 19 16:46:13 CST 2018
100 - test - Sun Aug 19 16:46:13 CST 2018
```

从输出结果可以看出，用实例内部成员的引用来改变实例的值是无效的，这个类是不可变类。

# 不可变类的优点
不可变类有两个主要有点，效率和安全。

## 效率
当一个对象是不可变的，那么需要拷贝这个对象的内容时，**就不用复制它的本身而只是复制它的地址**，`复制地址（通常一个指针的大小）只需要很小的内存空间`，具有非常高的效率。同时，对于引用该对象的其他变量也不会造成影响。

此外，`不变性保证了hashCode 的唯一性`，因此可以放心地进行缓存而不必每次重新计算新的哈希码。而哈希码被频繁地使用, 比如在hashMap 等容器中。将hashCode 缓存可以提高以不变类实例为key的容器的性能。

## 线程安全

在多线程情况下，一个可变对象的值很可能被其他进程改变，这样会造成不可预期的结果，而使用不可变对象就可以避免这种情况同时省去了同步加锁等过程，因此`不可变类是线程安全的`。

当然，不可变类也有缺点：不可变类的每一次“改变”都会产生新的对象，因此在使用中不可避免的会产生很多垃圾。

# String为什么不可变

1. `final` 修饰`String`类
防止继承后被value[]破坏
2. `private final char value[]`
2.1 final保证了value的应用不可变
2.2 private不允许外部访问value[]
2.3 string类内部没有再改变value[]


参考文章
[Java中的不可变类](http://zhiheng.me/124)
[如何理解 String 类型值的不可变？](https://www.zhihu.com/question/20618891)
[不可变类](https://www.jianshu.com/p/2080b524fb3a)