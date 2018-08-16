---
title: Java值传递
date: 2018-08-16 23:47:12
tags: 
- 面试反思
- 编程语言
categories: 面试反思
---
面试问到Java的传递。答出来了Java是call by value。但回答不好，现在详细分析。

<!-- more -->

# 概览

## 传递
这个问题一般是相对函数而言的，也就是java中的方法参数，两个专业术语：

- 按值调用（call by value）
- 按引用调用（call by reference）

## 区别
所谓的按值调用表示**方法接收的是调用着提供的值**，而按引用调用则表示**方法接收的是调用者提供的变量地址**(如果是C语言的话来说就是指针啦，当然java并没有指针的概念)。

**一个方法可以修改传递引用所对应的变量值，而不能修改传递值调用所对应的变量值**

Java程序设计语言确实是采用了按值调用，即call by value。也就是说方法得到的是所有参数值的一个拷贝，方法并不能修改传递给它的任何参数变量的内容。

# 例子分析

## 1. 基本数据类型

例子：

```
public class TestPass {
	private static int x = 10;

	public static void updateValue(int value) {
		value = 3 * value;
	}

	public static void main(String[] args) {
		System.out.println("调用前x的值：" + x);
		updateValue(x);
		System.out.println("调用后x的值：" + x);
	}
}
```

运行程序，结果如下：

```
调用前x的值：10
调用后x的值：10
```

可以看到**x的值并没有变化**，接下来我们一起来看一下具体的执行过程：

![基本数据类型](http://upload-images.jianshu.io/upload_images/11861611-67c9935c53838bb5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

分析：

1）value被初始化为x值的一个拷贝（也就是10）

2）**value被乘以3后等于30，但注意此时x的值仍为10！**

3）这个方法结束后，参数变量**value不再使用，被回收**。

结论：当传递方法参数类型为基本数据类型（数字以及布尔值）时，一个方法是不可能修改一个基本数据类型的参数。

## 2. 引用数据类型
当然java中除了基本数据类型还有引用数据类型，也就是对象引用，那么对于这种数据类型又是怎么样的情况呢？我们还是一样先来看一个例子：

声明一个User对象类型：

```
public class User {
	private String name;
	private int age;

	public User(String name, int age) {
		this.name = name;
		this.age = age;
	}

	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}

	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
}
```

### 调用执行类如下：(`toString()`)

```
public class TestUser {
	private static User user = null;

	public static void updateUser(User student) {
		student.setName("Lishen");
		student.setAge(18);
	}

	public static void main(String[] args) {
		user = new User("zhangsan", 26);
		System.out.println("调用前user的值：" + user.toString());
		updateUser(user);
		System.out.println("调用后user的值：" + user.toString());
	}

}
```

运行结果如下：

```
调用前user的值：test.User@532760d8
调用后user的值：test.User@532760d8
```

### 调用执行类如下：(`getName()`)

```
public class TestUser {
	private static User user = null;

	public static void updateUser(User student) {
		student.setName("Lishen");
		student.setAge(18);
	}

	public static void main(String[] args) {
		user = new User("zhangsan", 26);
		//System.out.println("调用前user的值：" + user.toString());
		System.out.println("调用前user的值：" + user.getName());
		updateUser(user);
		//System.out.println("调用后user的值：" + user.toString());
		System.out.println("调用前user的值：" + user.getName());
	}
}
```

运行结果如下：

```
调用前user的值：zhangsan
调用前user的值：Lishen
```

很显然，User的值被改变了，也就是说方法参数类型**如果是引用类型的话，引用类型对应的值将会被修改**

下面我们来分析一下这个过程：

![image](http://upload-images.jianshu.io/upload_images/11861611-3b8121d317427d30.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

过程分析：

1）student变量被初始化为user值的拷贝，这里是一个对象的引用。

2）调用student变量的set方法作用在这个引用对象上，user和student同时引用的User对象内部值被修改。

3）方法结束后，student变量不再使用，被释放，而user还是没有变，依然指向User对象。

结论：当传递方法参数类型为引用数据类型时，一个方法将修改一个引用数据类型的参数所指向对象的值。

虽然到这里两个数据类型的传递都分析完了，也明白的基本数据类型的传递和引用数据类型的传递区别，前者将不会修改原数据的值，而后者将会修改引用所指向对象的值。

**但是对象的地址不变。**

### 3. swap
可通过上面的实例我们可能就会觉得java同时拥有按值调用和按引用调用啊，可惜的是这样的理解是有误导性的，虽然上面引用传递表面上体现了按引用调用现象，但是Java中确实只有按值调用而没有按引用调用。到这里估计不少人都蒙逼了，下面我们通过一个反例来说明（回忆一下开头我们所说明的按值调用与按引用调用的根本区别）。

```
public class TestSwap {
	private static User user = null;
	private static User stu = null;

	public static void swap(User x, User y) {
		User temp = x;
		x = y;
		y = temp;
	}

	public static void main(String[] args) {
		user = new User("user", 26);
		stu = new User("stu", 18);
		System.out.println("调用前user的值：" + user.toString());
		System.out.println("调用前stu的值：" + stu.toString());
		swap(user, stu);
		System.out.println("调用后user的值：" + user.toString());
		System.out.println("调用后stu的值：" + stu.toString());

	}
}
```

我们通过一个swap函数来交换两个变量user和stu的值，在前面我们说过，如果是按引用调用那么一个方法可以修改传递引用所对应的变量值，也就是说如果java是按引用调用的话，那么swap方法将能够实现数据的交换，而实际运行结果是：

```
调用前user的值：test.User@532760d8
调用前stu的值：test.User@57fa26b7
调用后user的值：test.User@532760d8
调用后stu的值：test.User@57fa26b7
```

我们发现user和stu的值并没有发生变化，也就是方法并没有改变存储在变量user和stu中的对象引用。swap方法的参数x和y被初始化为两个对象引用的拷贝，这个方法交换的是这两个拷贝的值而已，最终，所做的事都是白费力气罢了。在方法结束后x，y将被丢弃，而原来的变量user和stu仍然引用这个方法调用之前所引用的对象。

![swap](http://upload-images.jianshu.io/upload_images/11861611-6fe4728aa2324b53.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个过程也充分说明了java程序设计语言对对象采用的不是引用调用，实际上是对象引用进行的是值传递，当然在这里我们可以简单理解为这就是按值调用和引用调用的区别，而且必须明白即使java函数在传递引用数据类型时，也只是拷贝了引用的值罢了，之所以能修改引用数据是因为它们同时指向了一个对象，但这仍然是按值调用而不是引用调用。

# 总结
1. 按值传递意味着当将一个参数传递给一个函数时，函数接收的是原始值的一个副本
2. 按引用传递意味着当将一个参数传递给一个函数时，函数接收的是原始值的内存地址，而不是值的副本
3. 对象是按引用传递的
- 一个方法不能修改一个基本数据类型的参数（数值型和布尔型）
- 一个方法可以修改一个引用所指向的对象状态，但这仍然是按值调用而非引用调用
（只是修改了属性，没有改动对象地址）
4. Java 有且仅有的一种参数传递机制，即按值传递


参考文章
[java中的参数传递——值传递、引用传递](https://www.cnblogs.com/perfy/archive/2012/10/16/2726039.html)
[java基本数据类型传递与引用传递区别详解](https://blog.csdn.net/javazejian/article/details/51192130)
