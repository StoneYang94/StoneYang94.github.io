---
title: Java泛型
date: 2018-05-20 23:44:41
tags: Java泛型
categories: 编程语言
---
将集合中的元素限定为一个特定的类型。
<!-- more -->
# 一. 为什么需要泛型？
## 泛型出现之前
远在 JDK 1.4 版本的时候，那时候是没有泛型的概念的。当时 Java 程序员们写集合类的代码都是类似于下面这样：

```java
List list = new ArrayList();
list.add("www.cnblogs.com");
list.add(23);
String name = (String)list.get(0);
Integer number = (Integer)list.get(1);
```

在代码中声明一个集合，我们可以往集合中放入各种各样的数据，而在**取出来的时候就进行强制类型转换**。但其实这样的代码存在一定隐患，因为可能过了不久我们就会**忘记**到底我们存放的 list 里面到底第几个是 String，第几个是 Integer 了。这样就会出现下面这样的情况：

```java
List list = new ArrayList();
list.add("www.cnblogs.com");
list.add(23);
String name = (String)list.get(0);
String number = (String)list.get(1);    //ClassCastException
```

-  编译时正常
开始时定义了一个List类型的集合，先向其中加入了一个字符串类型的值和一个Integer类型的值。这是完全允许的，因为此时list默认的类型为Object类型。
- 运行时异常
代码**编译阶段**正常，而**运行时**会现  ```“java.lang.ClassCastException”```  异常。并且，导致此类错误编码过程中不易发现。
## 分析问题
- 当我们将一个对象放入集合中，集合**不会记住**此对象的类型，当再次从集合中取出此对象时，改对象的编译类型变成了Object类型，但其运行时类型任然为其本身类型。
- 因此，取出集合元素时需要人为的强制类型转化到具体的目标类型，且很容易出现“java.lang.ClassCastException”异常。
## 寻找解决办法
那么有没有什么办法可以**使集合能够记住集合内元素各类型**，且能够达到只要编译时不出现问题，运行时就不会出现“java.lang.ClassCastException”异常呢？答案就是使用泛型。
# 什么是泛型
## 例子
```java
public class GenericDemo {
	public static void main(String[] args) {
		List<String> list = new ArrayList();
		list.add("datiangou");
		list.add("cimutongzi");
		for (int i = 0; i < list.size(); i++) {
			String name = list.get(i); 
			System.out.println("name "+i+" is " +name);
		}
	}
}

```

![泛型](http://upload-images.jianshu.io/upload_images/11861611-4d5f9c72314fc53f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 泛型就是将类型参数化，其在**编译时才确定具体的参数**。在上面这个例子中，这个具体的类型就是 String。可以看到我们在创建 List 集合的时候指定了 String 类型，这就意味着我们**只能**往 List 集合中存放 String 类型的数据。
- 而当我们指定泛型之后，我们去**取出数据**后就**不再需要进行强制类型转换**了，这样就减少了发生强制类型转换的风险。
## 术语
- ArrayList<E> – 泛型类型
- ArrayList – 原始类型
- E – 类型参数
- <> – 读作”typeof”


- ArrayList<Integer> – 参数化的类型
- Integer – 实际类型参数

#泛型的特性(擦除)

```java
public class GenericDemo {
	public static void main(String[] args) {
		ArrayList<String> a = new ArrayList<String>();
		ArrayList<Integer> b = new ArrayList<Integer>();
		Class c1 = a.getClass();
		Class c2 = b.getClass();
		System.out.println(c1 == c2);//true
	}
}
```

- 输出的结果是 true。
因为无论对于 ArrayList 还是 ArrayList，它们的 Class 类型都是一直的，都是  ``` ArrayList.class``` 。
- 那它们声明时指定的 String 和 Integer 到底体现在哪里呢？
答案是体现在类编译的时候。当 **JVM 进行类编译时**，会进行泛型检查，如果一个集合被声明为 String 类型，那么它往该集合存取数据的时候就会对数据进行判断，从而避免存入或取出错误的数据。
- 也就是说：泛型只存在于编译阶段，而不存在于运行阶段。**在编译后的 class 文件中，是没有泛型这个概念的。**

# 泛型的应用
泛型有三种使用方式，分别为：泛型类、泛型接口、泛型方法。
##  泛型类
泛型类型用于**类的定义**中，被称为泛型类。通过泛型可以完成对一组类的操作对外开放相同的接口。最典型的就是各种容器类，如：List、Set、Map。


```java
//定义-----------------
public class GenericDemo<T> {//此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型

	private T key;// key这个成员变量的类型为T,T的类型由外部指定

	public GenericDemo(T key) { // 泛型构造方法 形参key的类型也为T，T的类型由外部指定
		this.key = key;
	}

	public T getKey() { // 泛型方法getKey() 的返回值类型为T，T的类型由外部指定
		return key;
	}
//使用---------------------
	//在实例化泛型类时，必须指定T的具体类型
	public static void main(String[] args) {
		GenericDemo<Integer> genericInteger = new GenericDemo<Integer>(6666);
		GenericDemo<String> genericString = new GenericDemo<String>("datiangou");
		System.out.println("key of genericInteger is " + genericInteger.getKey());
		System.out.println("key of genericString is " + genericString.getKey());
	}

}
```

![image](http://upload-images.jianshu.io/upload_images/11861611-876f00441dc93d12.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
##  泛型接口

##  泛型方法


# 泛型通配符
## ”?”通配符
”?”表示任意类型，使用”?”通配符可以引用各种参数化的类型，可以调用与参数化无关的方法(如size()方法)，不能调用与参数化有关的方法(如add()方法)

## 扩展
### extends 限定通配符的上边界

``` java
ArrayList<? extends Number > collection1= new ArrayList<Integer >();//编译通过
```

```java
ArrayList<? extends Number > collection2= new ArrayList<String>();//编译不通过
```

### super 限定通配符的下边界
```java
ArrayList<? super Integer > collection3= new ArrayList<Number >();//编译通过
```

```java
ArrayList<? super Integer > collection4= new ArrayList<String>();//编译不通过
```

### 缺陷
- 对于 extends 通配符，我们无法向其中加入任何对象，但是我们可以进行正常的取出。extends 通配符偏向于内容的获取。
- 对于 super 通配符，我们可以存入 T 类型对象或 T 类型的子类对象，但是我们取出的时候只能用 Object 类变量指向取出的对象。super 通配符更偏向于内容的存入。
- 我们有一个 PECS 原则（Producer Extends Consumer Super）很好的解释了这两个通配符的使用场景。
  - Producer Extends 说的是当你的情景是生产者类型，需要获取资源以供生产时，我们建议使用 extends 通配符，因为使用了 extends 通配符的类型更适合获取资源。
  - Consumer Super 说的是当你的场景是消费者类型，需要存入资源以供消费时，我们建议使用 super 通配符，因为使用 super 通配符的类型更适合存入资源。
- 如果既想存入，又想取出，不要使用 extends 或 super 通配符。

参考文章
[大白话说Java泛型：入门、使用、原理](http://www.cnblogs.com/chanshuyi/p/deep_insight_java_generic.html)
[java 泛型详解-绝对是对泛型方法讲解最详细的，没有之一](https://blog.csdn.net/s10461/article/details/53941091)
[java基础巩固笔记(2)-泛型](https://brianway.github.io/2016/01/08/javase-learn-note-2-Generics/)
