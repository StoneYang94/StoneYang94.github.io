---
title: UML类图入门
date: 2018-05-10 21:36:11
tags: UML
categories: 基础工具
---
# UML初识
## [UML的维基百科的定义](https://zh.wikipedia.org/wiki/%E7%BB%9F%E4%B8%80%E5%BB%BA%E6%A8%A1%E8%AF%AD%E8%A8%80)
**统一建模语言**（英语：Unified Modeling Language，缩写 UML）是非专利的第三代[建模](https://zh.wikipedia.org/w/index.php?title=%E5%AF%B9%E8%B1%A1%E5%BB%BA%E6%A8%A1%E8%AF%AD%E8%A8%80&action=edit&redlink=1 "对象建模语言（页面不存在）")和[规约语言](https://zh.wikipedia.org/wiki/%E8%A7%84%E7%BA%A6%E8%AF%AD%E8%A8%80 "规约语言")。UML是一种开放的方法，用于说明、可视化、构建和编写一个正在开发的、面向对象的、软件密集系统的制品的开放方法。UML展现了一系列最佳工程实践，这些最佳实践在对大规模，复杂系统进行建模方面，特别是在软件架构层次已经被验证有效。
<!-- more -->
## UML图形分类
UML 2.2中一共定义了14种图示。
### 结构性图形（*Structure diagrams*）强调的是系统式的建模：
-  静态图（static diagram）
   -  [类图](https://zh.wikipedia.org/wiki/%E7%B1%BB%E5%9B%BE "类图")
   -  [对象图](https://zh.wikipedia.org/w/index.php?title=%E5%AF%B9%E8%B1%A1%E5%9B%BE&action=edit&redlink=1 "对象图（页面不存在）")
   - [包图](https://zh.wikipedia.org/w/index.php?title=%E5%8C%85%E5%9B%BE&action=edit&redlink=1 "包图（页面不存在）")
- 实现图（implementation diagram）
  -   [组件图](https://zh.wikipedia.org/wiki/%E7%BB%84%E4%BB%B6%E5%9B%BE "组件图")
  -   [部署图](https://zh.wikipedia.org/w/index.php?title=%E9%83%A8%E7%BD%B2%E5%9B%BE&action=edit&redlink=1 "部署图（页面不存在）")
  -  [剖面图](https://zh.wikipedia.org/w/index.php?title=%E5%89%96%E9%9D%A2%E5%9B%BE&action=edit&redlink=1 "剖面图（页面不存在）")
  -  [复合结构图](https://zh.wikipedia.org/wiki/%E5%A4%8D%E5%90%88%E7%BB%93%E6%9E%84%E5%9B%BE "复合结构图")
### 行为式图形（*Behavior diagrams*）强调系统模型中触发的事件：
- [活动图](https://zh.wikipedia.org/w/index.php?title=%E6%B4%BB%E5%8A%A8%E5%9B%BE&action=edit&redlink=1 "活动图（页面不存在）")
-  [状态图](https://zh.wikipedia.org/wiki/%E7%8A%B6%E6%80%81%E5%9B%BE "状态图")
-  [用例图](https://zh.wikipedia.org/wiki/%E7%94%A8%E4%BE%8B%E5%9B%BE "用例图")
### 交互性图形（*Interaction diagrams*），属于行为图形的子集合，强调系统模型中的资料流程：
- [通信图](https://zh.wikipedia.org/w/index.php?title=%E9%80%9A%E4%BF%A1%E5%9B%BE&action=edit&redlink=1 "通信图（页面不存在）")
-  [交互概述图](https://zh.wikipedia.org/wiki/%E4%BA%A4%E4%BA%92%E6%A6%82%E8%BF%B0%E5%9C%96 "交互概述图")（UML 2.0）
-  [时序图](https://zh.wikipedia.org/wiki/%E6%97%B6%E5%BA%8F%E5%9B%BE "时序图")（UML 2.0）
- [时间图](https://zh.wikipedia.org/w/index.php?title=%E6%97%B6%E9%97%B4%E5%9B%BE&action=edit&redlink=1 "时间图（页面不存在）")（UML 2.0）
# UML类图
## 为什么用类图
类图是软件工程的统一建模语言一种静态结构图，该图描述了系统的类集合，类的属性和类之间的关系。**帮助人们简化对系统的理解**，它是系统分析和设计阶段的重要产物，也是系统编码和测试的重要模型依据。学习好类图的绘制，是一位合格的软件工程师应有的技能。
## 类图组成
分析一段代码的类图，例如最简单的一个类Person 类有两个属性分别是name和age,并提供对应的get和set方法。java类代码如下：

```java
public class Person {
	private String name = "datiangou";
	private int age = 24;
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

![eclipse生成的UML类图](http://upload-images.jianshu.io/upload_images/11861611-64f8f9f693cbe618.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
可以看出类图由三部分组成，**类名，属性和操作方法**。
### 类名
顾名思义就是类的名字，对应于Java类中的类名，如果该类是抽象类的话，在类名右下角会有一个(Abstract)表示，假如Person是一个抽象类的话，
### 成员
#### 可见性
UML提供机制，以代表类的成员，如**属性和方法**，对他们的其他信息。
指定一个类成员（即任何属性或方法）的可见性有下列符号，必须摆在各成员的名字**之前**：

|可见性 |符号|
|---| ---|
|   public | \+  
|    private| \-    
|    protected| \#    
|    default | ~     

#### 属性名
通用表示方式:

> 可见性  名称:类型 [ = 默认值 ]

#### 方法
通用表示方式:

> 可见性  名称(参数列表) [ : 返回类型]

## 类图关系
本小节转载自[ [UML类图几种关系的总结，泛化 = 实现 > 组合 > 聚合 > 关联 > 依赖](http://www.cnblogs.com/jiqing9006/p/5895446.html)
](https://www.cnblogs.com/jiqing9006/p/5895446.html)
在UML类图中，常见的有以下几种关系: 泛化（Generalization）, 实现（Realization），关联（Association)，聚合（Aggregation），组合(Composition)，依赖(Dependency)
### 泛化（Generalization）
- 泛化关系：是一种继承关系，表示一般与特殊的关系，它指定了子类如何特化父类的所有特征和行为。例如：老虎是动物的一种，即有老虎的特性也有动物的共性。
- 箭头指向：带三角箭头的实线，箭头指向父类
![泛化](http://upload-images.jianshu.io/upload_images/11861611-e5b29e2b6a66ee7f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 实现（Realization）
- 实现关系：是一种类与接口的关系，表示类是接口所有特征和行为的实现.
- 箭头指向：带三角箭头的虚线，箭头指向接口
![实现](http://upload-images.jianshu.io/upload_images/11861611-eeefa9074e28871c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 关联（Association)
- 关联关系：是一种拥有的关系，它使一个类知道另一个类的属性和方法；如：老师与学生，丈夫与妻子关联可以是双向的，也可以是单向的。双向的关联可以有两个箭头或者没有箭头，单向的关联有一个箭头。
- 代码体现：成员变量
- 箭头及指向：带普通箭头的实心线，指向被拥有者
![关联](http://upload-images.jianshu.io/upload_images/11861611-c3fc8c243e6eabf8.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 聚合（Aggregation）
- 聚合关系：是整体与部分的关系，且部分可以离开整体而单独存在。如车和轮胎是整体和部分的关系，轮胎离开车仍然可以存在。聚合关系是关联关系的一种，是强的关联关系；关联和聚合在语法上无法区分，必须考察具体的逻辑关系。
- 代码体现：成员变量
- 箭头及指向：带空心菱形的实心线，菱形指向整体
![聚合](http://upload-images.jianshu.io/upload_images/11861611-182277817bcdd249.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 组合(Composition)
- 组合关系：是整体与部分的关系，但部分不能离开整体而单独存在。如公司和部门是整体和部分的关系，没有公司就不存在部门。
-组合关系是关联关系的一种，是比聚合关系还要强的关系，它要求普通的聚合关系中代表整体的对象负责代表部分的对象的生命周期。
- 代码体现：成员变量
- 箭头及指向：带实心菱形的实线，菱形指向整体
![组合](http://upload-images.jianshu.io/upload_images/11861611-4de60846eb607166.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 依赖(Dependency)
- 依赖关系：是一种使用的关系，即一个类的实现需要另一个类的协助，所以要尽量不使用双向的互相依赖.
- 代码表现：局部变量、方法的参数或者对静态方法的调用
- 箭头及指向：带箭头的虚线，指向被使用者
![依赖](http://upload-images.jianshu.io/upload_images/11861611-16cb963d95fe3499.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 总结
![直观图](http://upload-images.jianshu.io/upload_images/11861611-71ce898792a8e283.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


参考文章
[维基百科类图](https://zh.wikipedia.org/wiki/%E9%A1%9E%E5%88%A5%E5%9C%96)
[Java利器之UML类图详解](https://blog.csdn.net/xiehuimx/article/details/53427452)
[# [UML类图几种关系的总结，泛化 = 实现 > 组合 > 聚合 > 关联 > 依赖](http://www.cnblogs.com/jiqing9006/p/5895446.html)
](https://www.cnblogs.com/jiqing9006/p/5895446.html)