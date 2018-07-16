---
title: Spring入门
date: 2018-05-04 22:33:04
tags: Spring入门 
categories: Spring 
---
说明：本文主要内容来自[慕课网](http://www.imooc.com/)。配合[视频](http://www.imooc.com/learn/196)食用口味更佳。
主要是顺着已经学习的视频顺序总结一遍，以深化理解和方便日后复习。
本部分只包含IOC和AOP。

<!-- more -->

# 第一章 概述

## Spring 入门课程简介

本门课程包含的内容：
Spring简介
IOC（配置、注解）
Bean（配置、注解）
AOP（配置、注解、AspetJ、API）


## Spring概况

### Spring是什么
- Spring是一个开源框架，最初为了解决企业应用开发的复杂性而创建的，但现在已经不止应用于企业应用
- 是一个轻量级的控制反转（IOC）和面向切面（AOP）的容器框架
  - 从**大小与开销**两方面而言Spring都是轻量的
  - 通过控制反转（IOC）的技术达到**松耦合**的目的
  - 提供了面向切面编程(AOP)的丰富支持，允许通过**分离**应用的业务逻辑与系统级服务进行内聚性的开发
  - 包含并管理应用对象的配置和生命周期，这个意义上是一种**容器。**
  - 将简单的组件配置、组合成为复杂的应用，这个意义上是**框架**

### 为什么是Spring
- 在Spring上开发应用简单
- 在Spring上开发应用方便
- 在Spring上开发应用快捷

### Spring作用
- 容器
- 提供了多种技术的支持
    --JMS
    --MQ支持
    --UnitTest
- AOP（事务管理、日志等）
- 提供了众多方便应用的辅助类（JDBC Template等）
- 对主流应用框架（Hibernate等）提供了良好的支持

### 适用范围
- 构建企业应用（SpringMVC+Spring+Hibernate/MyBatis）
- 单独使用Bean容器（Bean管理）
- 单独使用AOP进行切面处理
- 其他的Spring功能：如：对消息的支持等
- 在互联网中的应用

## Spring框架

![Spring框架总览](http://upload-images.jianshu.io/upload_images/11861611-6c924d3183ec90b0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 框架

#### 什么是框架
> 维基百科：软件框架，通常是指为了实现某个业界标准或者完成特定基本任务的软件组件**规范**，也指为了实现某个软件组织规范时，提供规范所要求之基础功能的软件产品。

通俗的说，框架就是制定一套规范或者规则（思想），大家（程序员）在该规范或者规程（思想）下工作。用现实生活中的比喻就是：使用别人搭好的舞台，你来做表演。

#### 框架的特点
- 半成品
- 封装了特定的处理流程和控制逻辑
-  成熟的、不断升级的软件

#### 框架与类库的区别 
-  框架一般是**封装了逻辑、高内聚的**，类库则是**松散**的工具组合。
- 框架专注于某一领域，类库则是更通用的。

![框架于类库区别](http://upload-images.jianshu.io/upload_images/11861611-a831defae2e18a01.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 类库通过不同的方式组装成不同的框架

#### 为什么使用框架
- 软件系统日趋**复杂**
-  **重用度高**，开发效率和质量提高
- 软件设计人员要专注于对领域的了解，使需求分析更充分
- 易于上手、快速解决问题

# 第二章 Spring IOC 容器

## 接口与面向接口编程

### 接口
- 用于沟通的中介物的抽象化
- 实体把自己提供给外界的一种**抽象化说明**，用以由内部操作**分离**出外部沟通方式，使其能被修改内部而不影响外界其他实体与其交互的方式。
- 对应Java接口即声明，声明了哪些方法对外提供的。
- 在Java8中，接口可以拥有方法体

### 面向接口编程
- ***结构设计中，分清层次及调用关系，每层只向外（上层）提供一组功能接口，各层间仅依赖接口而非实现类***
- 接口实现的**变动不影响**各层间的调用，这一点在公共服务中尤为重要
- “面向接口编程”中的“接口”是用于**隐藏**具体实现和实现多态性的组件 

## 什么是IOC

### 站在过程的角度看
IOC：控制反转，控制权的转移，不再是`应用程序本身-->依赖对象的创建和维护`，而是由`外部容器-->依赖对象的创建和维护`

### 站在主体的角度看
DI（依赖注入）是其中一种实现方式
2004年，Martin Fowler探讨了同一个问题，既然IoC是控制反转，那么 到底是“哪些方面被控制反转了呢？”，经过详细的分析和论证后，他得出了答案：“`获得依赖对象的过程`被反转了”。控制被反转之后，获得依赖对象的过程由自身管理变成了由IOC容器的主动注入。于是，他给“控制反转” 取了一个更合适的名字“依赖注入（Dependency Injection）”。他的这个答案，实际上给出了实现IoC的方法：注入。
**所谓依赖注入，就是由`IoC容器 在运行期间`，动态地将某种依赖关系注入到对象之中。**

### 目的
创建对象并且组装对象之间的关系

## IOC简单类比

|通过中介找房子   | 通过IOC使用对象|
|---|---|
|找中介 |  找IOC容器|
|中介介绍房子 | 容器返回对象|
|租房、入住|  使用对象|

## Spring的Bean配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd" >
     
    //这是我们要配置的内容
    <bean id="oneInterface" class="com.imooc.ioc.interfaces.OneInterfaceImpl"></bean>

 </beans>
```

[Java bean是什么](https://www.zhihu.com/question/19773379)

## Bean容器初始化

### 基础
两个包：
- org.springframework.beans
- org.springframework.context

容器：
- BeanFactory提供配置结构和基本功能，加载并初始化Bean
- ApplicationContext保存了Bean对象，并在Spring中广泛使用

BeanFactory和ApplicationContext是Spring两种很重要的容器,前者提供了最基本的依赖注入的支持，而后者在继承前者的基础进行了功能的拓展，例如增加了事件传播，资源访问和国际化的消息访问等功能。

### 方式---`ApplicationContext`

#### 1.  本地文件
` FileSystemXmlApplicationContext`

```java
FileSystemXmlApplicationContext context = new 
FileSystemXmlApplicationContext("F:/workspace/appcontext.xml");
```

#### 2.  类路径  
`ClassPathXmlApplicationContext`

```
ClassPathXmlApplicationContext context = new
ClassPathXmlApplicationContext("classpath:spring-context.xml");
```

#### 3. Web应用
依赖`servlet`或`Listener  `

```xml
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderServlet</listener-class>
</listener>
<servlet>
    <servlet-name>context</servlet-name>
    <servlet-class>org.springframework.web.context.ContextLoaderServlet</servlet-class>
    <load-on-startup>on</load-on-startup>
</servlet>
```

##  Spring注入方式
Spring注入是指在启动Spring容器加载bean配置的时候，完成对变量的**赋值行为**。常用的两种注入方式`设值注入`与`构造注入`。

### 设值注入---`property `
通过setter注入。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd" >

      <bean id="injectionService" class="com.imooc.ioc.injection.service.InjectionServiceImpl">
            <property name="injectionDAO" ref="injectionDAO"></property>
      </bean>


      <bean id="injectionDAO" class="com.imooc.ioc.injection.dao.InjectionDAOImpl"></bean>

 </beans>
```

### 构造注入---`constructor-arg`
通过构造器注入。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd" >


        <bean id="injectionService" class="com.imooc.ioc.injection.service.InjectionServiceImpl">
            <constructor-arg name="injectionDAO" ref="injectionDAO"></constructor-arg>
        </bean>

        <bean id="injectionDAO" class="com.imooc.ioc.injection.dao.InjectionDAOImpl"></bean>

 </beans>
```

# 第三章：Spring Bean装配（上）

## Bean的配置项及作用域

### Bean配置项

|属性|描述|
|--|--|
|id\|name|  这个属性指定`唯一`的 bean 标识符。在基于 XML 的配置元数据中，可以使用 id 或 name 属性来指定 bean 标识符
|class|这个属性是强制性的，并且指定用来创建 bean 的` bean 类`
|scope|这个属性指定由特定的 bean 定义创建的对象的`作用域`
|constructor-arg|用来注入依赖关系
|properties|用来注入依赖关系
|autowiring mode    |用来注入依赖关系
|lazy-initialization mode|延迟初始化的 bean 告诉 IoC 容器在它`第一次被请求时`，而不是在启动时去创建一个 bean 实例
|initialization()|在 bean 的所有必需的属性被容器`设置`之后，调用回调方法
|destruction()|当包含该 bean 的容器被`销毁`时，使用回调方法

### Bean的作用域
添加方式

```xml
<bean id=" " class=" " scope="singleton"></bean>
```

|作用域|描述|
|--|--|
|singleton|单例，指`一个Bean容器中只存在一份`
|prototype|`每次请求`（每次使用）创建新的实例，destroy方式不生效
|request|`每次http请求`创建一个实例且仅在当前request内有效
|session|同上，每次http请求创建，当前session内有效
|global session|基于portlet的web中有效（portlet定义了global session），如果在web中，同session

## Bean的生命周期

![ Bean的生命周期](http://p7vxw6hv7.bkt.clouddn.com/18-7-13/21802297.jpg)

1. 首先容器启动后，会对scope为singleton且非懒加载的bean进行实例化
2. 按照Bean定义信息配置信息，注入所有的属性
3. 如果Bean实现了BeanNameAware接口，会回调该接口的`setBeanName()方法`，传入该Bean的id，此时该Bean就获得了自己在配置文件中的id
4. 如果Bean实现了BeanFactoryAware接口,会回调该接口的`setBeanFactory()方法`，传入该Bean的BeanFactory，这样该Bean就获得了自己所在的BeanFactory
5. 如果Bean实现了ApplicationContextAware接口,会回调该接口的`setApplicationContext()方法`，传入该Bean的ApplicationContext，这样该Bean就获得了自己所在的ApplicationContext，
6. 如果有Bean实现了BeanPostProcessor接口，则会回调该接口的`postProcessBeforeInitialzation()方法`
7. 如果Bean实现了InitializingBean接口，则会回调该接口的`afterPropertiesSet()方法`
8. 如果Bean配置了init-method方法，则会执行`init-method配置的方法`
9. 如果有Bean实现了BeanPostProcessor接口，则会回调该接口的`postProcessAfterInitialization()方法`
10.经过流程9之后，就可以正式使用该Bean了,对于scope为singleton的Bean,Spring的ioc容器中会缓存一份该bean的实例，而对于scope为prototype的Bean,每次被调用都会new一个新的对象，期生命周期就交给调用方管理了，不再是Spring容器进行管理了
11.容器关闭后，如果Bean实现了DisposableBean接口，则会回调该接口的`destroy()方法`
12.如果Bean配置了destroy-method方法，则会执行`destroy-method配置的方法`

至此，整个Bean的生命周期结束

### 创建与销毁

#### 方法1：实现InitializingBean和DisposableBean接口

这两个接口都只包含一个方法。
- 实现`InitializingBean`接口的`afterPropertiesSet()`方法可以在Bean属性值设置好之后做一些操作
- 实现`DisposableBean`接口的`destroy()`方法可以在销毁Bean之前做一些操作。

如下：

```java
public class GiraffeService implements InitializingBean,DisposableBean {
    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("执行InitializingBean接口的afterPropertiesSet方法");
    }
 
    @Override
    public void destroy() throws Exception {
        System.out.println("执行DisposableBean接口的destroy方法");
    }
}
```

**这种方法比较简单，但是不建议使用。**因为这样会将Bean的实现和Spring框架`耦合`在一起。

#### 方法2：在bean的配置文件中指定init-method和destroy-method方法

Spring允许我们创建自己的init方法和destroy方法，只要在Bean的配置文件中指定`init-method`和`destroy-method`的值就可以在Bean初始化时和销毁之前执行一些操作。

配置文件中的配置：

```xml
<bean name="giraffeService" class="com.giraffe.spring.service.GiraffeService" 
                            init-method="initMethod" destroy-method="destroyMethod">
</bean>
```

代码：

```java
public class GiraffeService {
    //通过<bean>的destroy-method属性指定的销毁方法
    public void destroyMethod() throws Exception {
        System.out.println("执行配置的destroy-method");
    }
 
    //通过<bean>的init-method属性指定的初始化方法
    public void initMethod() throws Exception {
        System.out.println("执行配置的init-method");
    }
}
```

需要注意的是自定义的init-method和post-method方法可以抛异常但是不能有参数。
**这种方式比较推荐**，因为可以自己创建方法，`无需将Bean的实现直接依赖于spring的框架`。

#### 方法3：使用@PostConstruct和@PreDestroy注解
- 除了xml配置的方式，Spring也支持用`@PostConstruct`和 `@PreDestroy`注解来指定init和destroy方法。
- 这两个注解均在`javax.annotation`包中。
- 为了注解可以生效，需要在配置文件中定义`org.springframework.context.annotation.CommonAnnotationBeanPostProcessor`或`context:annotation-config`

配置文件:

```xml
<bean class="org.springframework.context.annotation.CommonAnnotationBeanPostProcessor" />
```

代码：

```java
public class GiraffeService {
    @PostConstruct
    public void initPostConstruct(){
        System.out.println("执行PostConstruct注解标注的方法");
    }
 
    @PreDestroy
    public void preDestroy(){
        System.out.println("执行preDestroy注解标注的方法");
    }
 
}
```

#### 方法4：配置全局默认初始化、销毁方法

` default-init-method=" "`
` default-destroy-method=" "`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd" 
        default-init-method="defautInit" default-destroy-method="defaultDestroy">

 </beans>
```

### Bean的生命周期示例

- 定义一个`Person`类
- 实现了`BeanNameAware,BeanFactoryAware,ApplicationContextAware,InitializingBean,DisposableBean`五个接口
- 在applicationContext.xml文件中配置了该Bean的`id`为person1,并且配置了`init-method`和`destroy-method`,为该Bean配置了属性`name`为jack的值
- 然后定义了一个MyBeanPostProcessor方法,该方法实现了BeanPostProcessor接口，且在applicationContext.xml文件中配置了该方法的Bean,其代码如下所示:


```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
    xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
            http://www.springframework.org/schema/beans 
            http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
            http://www.springframework.org/schema/context
            http://www.springframework.org/schema/context/spring-context-3.2.xsd">
                    
     <bean id="person1" destroy-method="myDestroy" 
            init-method="myInit" class="com.test.spring.life.Person">
        <property name="name">
            <value>jack</value>
        </property>
    </bean>
    
    <!-- 配置自定义的后置处理器 -->
     <bean id="postProcessor" class="com.pingan.spring.life.MyBeanPostProcessor" />
</beans>
```

```java
public class Person implements BeanNameAware, BeanFactoryAware,
        ApplicationContextAware, InitializingBean, DisposableBean {

    private String name;   
    public Person() {
        System.out.println("PersonService类构造方法");
    }
    
    
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
        System.out.println("set方法被调用");
    }

    //自定义的初始化函数
    public void myInit() {
        System.out.println("myInit被调用");
    }
    
    //自定义的销毁方法
    public void myDestroy() {
        System.out.println("myDestroy被调用");
    }

    public void destroy() throws Exception {
        // TODO Auto-generated method stub
     System.out.println("destory被调用");
    }

    public void afterPropertiesSet() throws Exception {
        // TODO Auto-generated method stub
        System.out.println("afterPropertiesSet被调用");
    }

    public void setApplicationContext(ApplicationContext applicationContext)
            throws BeansException {
        // TODO Auto-generated method stub
       System.out.println("setApplicationContext被调用");
    }

    public void setBeanFactory(BeanFactory beanFactory) throws BeansException {
        // TODO Auto-generated method stub
         System.out.println("setBeanFactory被调用,beanFactory");
    }

    public void setBeanName(String beanName) {
        // TODO Auto-generated method stub
        System.out.println("setBeanName被调用,beanName:" + beanName);
    }
    
    public String toString() {
        return "name is :" + name;
    }
```

```java
public class MyBeanPostProcessor implements BeanPostProcessor {

    public Object postProcessBeforeInitialization(Object bean,
            String beanName) throws BeansException {
        // TODO Auto-generated method stub
        
        System.out.println("postProcessBeforeInitialization被调用");
        return bean;
    }

    public Object postProcessAfterInitialization(Object bean,
            String beanName) throws BeansException {
        // TODO Auto-generated method stub
        System.out.println("postProcessAfterInitialization被调用");
        return bean;
    }

}
```

```java
public class AcPersonServiceTest {

    public static void main(String[] args) {
        // TODO Auto-generated method stub

        System.out.println("开始初始化容器");
        ApplicationContext ac = new ClassPathXmlApplicationContext("com/test/spring/life/applicationContext.xml");
        
        System.out.println("xml加载完毕");
        Person person1 = (Person) ac.getBean("person1");
        System.out.println(person1);        
        System.out.println("关闭容器");
        ((ClassPathXmlApplicationContext)ac).close();
        
    }
}
```

我们启动容器，可以看到整个调用过程：

>开始初始化容器
九月 25, 2016 10:44:50 下午 org.springframework.context.support.ClassPathXmlApplicationContext prepareRefresh
信息: Refreshing org.springframework.context.support.ClassPathXmlApplicationContext@b4aa453: startup date [Sun Sep 25 22:44:50 CST 2016]; root of context hierarchy
九月 25, 2016 10:44:50 下午 org.springframework.beans.factory.xml.XmlBeanDefinitionReader loadBeanDefinitions
信息: Loading XML bean definitions from class path resource [com/test/spring/life/applicationContext.xml]
Person类构造方法
set方法被调用
setBeanName被调用,beanName:person1
setBeanFactory被调用,beanFactory
setApplicationContext被调用
postProcessBeforeInitialization被调用
afterPropertiesSet被调用
myInit被调用
postProcessAfterInitialization被调用
xml加载完毕
name is :jack
关闭容器
九月 25, 2016 10:44:51 下午 org.springframework.context.support.ClassPathXmlApplicationContext doClose
信息: Closing org.springframework.context.support.ClassPathXmlApplicationContext@b4aa453: startup date [Sun Sep 25 22:44:50 CST 2016]; root of context hierarchy
destory被调用
myDestroy被调用


##  Aware接口
有些时候我们需要在Bean的初始化中使用Spring框架自身的一些对象来执行一些操作，比如获取ServletContext的一些参数，获取ApplicaitionContext中的BeanDefinition的名字，获取Bean在容器中的名字等等。为了让Bean可以**获取到框架自身的一些对象**，Spring提供了一组名为`___Aware`的**接口**。


这些接口均继承于org.springframework.beans.factory.Aware标记接口，并提供一个将由Bean实现的`set___()`方法,Spring通过基于setter的依赖注入方式使相应的对象可以被Bean使用。


一些重要的Aware**接口**：

|接口 |作用|
|--|--|
|ApplicationContextAware|获得ApplicationContext对象,可以用来获取所有Bean definition的名字
|BeanFactoryAware|获得BeanFactory对象，可以用来检测Bean的作用域
|BeanNameAware|获得Bean在配置文件中定义的名字
|ResourceLoaderAware|获得ResourceLoader对象，可以获得classpath中某个文件
|ServletContextAware|在一个MVC应用中可以获取ServletContext对象，可以读取context中的参数
|ServletConfigAware|在一个MVC应用中可以获取ServletConfig对象，可以读取config中的参数

##  自动装配（Autowiring）
具体参考
[博客](https://www.cnblogs.com/leiOOlei/p/3548290.html)
[为什么Spring要支持Autowire（自动装配）](http://www.importnew.com/24731.html)

### 什么是装配
在spring中，对象无需自己查找或创建与其关联的其他对象，容器负责把需要相互协作的对象引用赋予各个对象。
而**创建对象之间协作关系的行为**通常称为装配。

### Spring 装配的方式
1. 隐式Bean的发现机制和自动装配
所谓自动装配，就是将一个`Bean`注入到`其他Bean的Property`中，类似于以下：

```xml
<bean id="customer" class="com.lei.common.Customer" autowire="byName" />
```

2. 显式装配：
    - 在Java中显式装配
    - 在XML中显式装配

### 为什么自动装配
Spring引入Autowire（自动装配）机制就是为了解决`<bean>`标签下`<property>`标签过多的问题

### 5种自动装配模式
| 模式 | 描述 |
|---|---|
| no | 默认情况下，不自动装配，通过“ref”attribute手动设定 |
| byName | 由属性名自动装配。根据Property的Name自动装配，如果一个bean的name，和另一个bean中的Property的name相同，则自动装配这个bean到Property中|
| byType | 由属性数据类型自动装配。根据Property的数据类型（Type）自动装配，如果一个bean的数据类型，兼容另一个bean中Property的数据类型，则自动装配 |
| constructor | 根据构造函数参数的数据类型，进行`byType`模式的自动装配 |
| autodetect | 如果发现默认的构造函数，用`constructor`模式，否则，用`byType`模式 |

## Resource

### 简介
在Spring内部实现机制，针对于资源文件(配置的`xml文件`)有一个统一的接口Resource

### 常用方法

|方法|说明|
| --| --| 
|exists()|用于判断对应的资源`是否存在`
|  isReadable()|用于判断对应资源的内容`是否可读`。需要注意的是当其结果为true的时候，其内容未必真的可读，但如果返回false，则其内容必定不可读。
|isOpen()|用于判断当前资源是否代表一个已打开的输入流，如果结果为true，则表示当前资源的输入流不可多次读取，而且在读取以后需要对它进行关闭，以防止内存泄露。该方法主要针对于InputStreamResource，实现类中只有它的返回结果为true，其他都为falses
|getURL()|返回当前资源对应的URL。如果当前资源不能解析为一个URL则会抛出异常。如ByteArrayResource就不能解析为一个URL
|getFile()|返回当前资源对应的File。如果当前资源不能以绝对路径解析为一个File则会抛出异常。如ByteArrayResource就不能解析为一个File。
|getInputStream()|获取当前资源代表的输入流。除了InputStreamResource以外，其它Resource实现类每次调用getInputStream()方法都将返回一个全新的InputStream。

### 常用实现类

|实现类|说明|
| --| --| 
|ClassPathResource|获取`类路径下`的资源文件。假设有一个资源文件test.txt在类路径下，`new ClassPathResource("test.txt")`
|FileSystemResource|获取`文件系统`里面的资源。
|UrlResource|可用来`代表URL对应的资源`，它对URL做了一个简单的封装。通过给定一个URL地址，我们就能构建一个UrlResource。
|ByteArrayResource|针对于`字节数`组封装的资源，它的构建需要一个字节数组。
|ServletContextResource|ServletContextResource持有一个ServletContext的引用，其底层是通过ServletContext的getResource()方法和getResourceAsStream()方法来获取资源的
|InputStreamResource|针对于输入流封装的资源，它的构建需要一个输入流



# 第四章 注解

Spring的一个核心功能是IOC，就是将Bean初始化加载到容器中，Bean是如何加载到容器的，可以使用Spring注解方式或者Spring XML配置方式。 
Spring注解方式减少了配置文件内容，更加便于管理，并且使用注解可以大大提高了开发效率。
下面安装分类讲解Spring中常用的一些注解。

## 将普通类加入容器形成Bean
spring使用配置文件或者注解的方式进行标识需要处理的java类，从而知道`哪些Java类当bean类处理`。

|注解|标注|
|--|--|
|@Component |标准一个普通的spring Bean类| 
|@Repository|标注一个DAO组件类| 
|@Service|标注一个业务逻辑组件类| 
|@Controller|标注一个控制器组件类|

其中@Component、@Repository、@Service、@Controller实质上属于同一类注解，用法相同，功能相同，区别在于标识组件的类型。

## 从容器中取Bean（装配bean）

|注解|标注|
|--|--|
|@Autowired|属于Spring 的org.springframework.beans.factory.annotation包下,可用于为类的属性、构造器、方法进行注值 
|@Resource|不属于spring的注解，而是来自于JSR-250位于java.annotation包下，使用该annotation为目标bean指定协作者Bean。 
|@PostConstruct 和 @PreDestroy 方法|实现初始化和销毁bean之前进行的操作 

## Spring MVC模块注解

### @Controller---`与前端交互`
表明该类会作为**与前端作交互的控制层组件**
- 通过服务接口定义的提供访问应用程序的一种行为，`解释`用户的输入，将其`转换`成一个模型然后将试图呈献给用户

```java
@Controller
public class HappyController {
    //do something
}
```

### @RequestMapping---`url --> 类或方法` 
-  这个注解用于将url`映射`到**整个处理类或者特定的处理请求的方法**
- 可以只用通配符！
- @RequestMapping中可以使用 `method =   `属性标记其所接受的方法类型，如果不指定方法类型的话，可以使用 HTTP GET/POST 方法请求数据，但是一旦指定方法类型，就只能使用该类型获取数据

```java
@Controller
@RequestMapping("/happy")
public class HappyController  {

  @Autowired
  private HappyService happyService;

  @RequestMapping(/hello/ *)
  public void sayHello(){
        //请求为 /happy/hello/ * 都会进入这个方法！
        //例如：/happy/hello/123   /happy/hello/adb
        //可以通过get/post 请求
  }

  @RequestMapping(value="/haha",method=RequestMethod.GET)
  public void sayHaHa(){
  //只能通过get请求
  }

}
```

# 第五章：Spring AOP

具体参考[博客](https://blog.csdn.net/jeffleo/article/details/54136904)

## AOP基本概念及特点

### 什么是AOP
`AOP(Aspect Oriented Programming)`意为：面向切面编程，通过`预编译`方式和`运行期动态代理`实现程序功能的**统一维护**的一种技术。

主要功能是：日志记录，性能统计，安全控制，事务处理，异常处理等

### AOP实现方式
- 预编译 
AspectJ
- 运行期间动态代理（JDK动态代理、CGLib动态代理） 
SpringAOP、JbossAOP
### AOP几个关键概念

|名称|说明|举例
|---|---|--|
|切面（Aspect）|散落在系统各处的通用的`非业务代码`|日志模块，权限模块，事务模块
|连接点（Joinpoint）|被拦截到的点|Spring只支持方法类型的连接点，所以在Spring中连接点指的就是被拦截到的方法
|通知（Advice）|拦截到连接点之后要执行的代码|分为前置、后置、异常、最终、环绕通知五类
|切入点（Pointcut）|拦截的方法，连接点拦截后变成切入点|带有通知的连接点
|织入（weave）|通过切入点切入，将切面应用到目标对象并导致代理对象创建的过程|&nbsp;
|目标对象（Target Object）    |代理的目标对象，指要织入的对象模块|`业务代码`
|AOP代理（AOP Proxy）   |AOP框架创建的对象（包括通知方法执行等功能）|Spring中的AOP代理可以使JDK动态代理，也可以是CGLIB代理，前者基于接口，后者基于子类

## 各关键概念具体介绍

### Advice

说明：

|类型|说明
|---|---|
|前置通知（Before advcie）|   在某连接点（join point）之前执行的通知，但不能阻止连接点前的执行（除非它抛出一个异常）
|返回后通知（After returning advice）  |在某连接点（join point）正常执行完成后执行的通知
|抛出异常后通知（After throwing advice）|    在方法抛出异常退出时执行的通知
|后通知（After（finally）advice）| 当某个连接点退出的时候执行的通知（不论是正常返回还是异常退出）
|环绕通知（Around Advice）    |包围一个连接点（join point）的通知

使用方式：

|名称|配置中使用|注解中使用
|---|---|---|
|前置通知（Before advcie）|   <aop:aspect>里面使用<aop:before>|@Before
|返回后通知（After returning advice）  |<aop:aspect>里面使用<aop:after-returning>|@AfterReturning
|抛出异常后通知（After throwing advice）|<aop:aspect>里面使用<aop:after-throwing>|@AfterThrowing
|后通知（After（finally）advice）| aop:aspect>里面使用<aop:after>元素|@After
|环绕通知（Around Advice）    |<aop:aspect>里面使用<aop:around>|@Around|

通知执行顺序：

```
前置通知→环绕通知连接点之前→连接点执行  1→  环绕通知连接点之后→返回通知→后通知
                                     2→  (如果发生异常)异常通知→后通知
```

### pointcut

|切入点表达式|说明
|--|--|
|execution|用于匹配方法执行的`连接点`
|within|用于匹配`指定类型内`的方法执行
|this|用于匹配当前`AOP代理对象类型`的执行方法；this中使用的表达式必须是完整类名，不支持通配符
|target|用于匹配`当前目标对象类型`的执行方法；注意是目标对象的类型匹配，这样就不包括引入接口也类型匹配；注意target中使用的表达式必须是完整类名，不支持通配符；
|args|用于匹配`当前执行的方法传入的参数为指定类型`的执行方法；参数类型列表中的参数必须是完整类名，通配符不支持；args属于动态切入点，这种切入点开销非常大，非特殊情况最好不要使用；
|@within|用于匹配所有持有指定注解类型内的方法；注解类型也必须是完整类名；
|@target|用于匹配当前目标对象类型的执行方法，其中目标对象持有指定的注解；注解类型也必须是完整类名；
|@args|用于匹配当前执行的方法传入的参数持有指定注解的执行；注解类型也必须是完整类名；
|@annotation|用于匹配当前执行方法持有指定注解的方法；注解类型也必须是完整类名；
|bean|Spring AOP扩展的，AspectJ没有对于指示符，用于匹配特定名称的Bean对象的执行方法；
|reference pointcut|表示引用其他命名切入点，只有注解风格支持，XML风格不支持。

#### 定义
  - 一个切入点通过一个普通的方法定义来提供，使用@Pointcut注解
  - 方法返回类型必须为void

- eg.
定义一个名为‘anyOldTransfer’，这个切点将匹配任何名为”transfer“的方法执行@Pointcut("execution(* transfer(..))")//the pointcut expression

```java
@Pointcut("execution(* transfer(..))")//the pointcut expression
private void anyOldTransfer(){}//the pointcut signature
```

#### 组合pointcut
- 切入点表达式可以通过`&&、||、!`进行组合，也可以通过名字引入切入点表达式
- 通过组合，可以建立更加复杂的切入点表达式

```java
@Pointcut("execution(public * (..))")
private void anyPublicOperation(){}

@Pointcut("within(com.xyz.someapp.trading...)")
private void inTrading(){}

@Pointcut("anyPublicOperation() && inTrading()")
private void tradingOperation(){}
```

### 匹配语法

|符号|匹配|
|--|--|
|* |匹配`任何数量字符`
|.. |匹配`任何数量字符的重复`，如在类型模式中匹配任何数量子包；而在方法参数模式中匹配任何数量参数。
|+ |匹配`指定类型的子类型`；仅能作为后缀放在类型模式后边。



|例子|匹配|不匹配
|--|--|--|
|java.lang.String |  匹配String类型|&nbsp;
|java.*.String  | 匹配java包下的任何“一级子包”下的String类型；如匹配java.lang.String|但不匹配java.lang.ss.String|
|java..*  | 匹配java包及任何子包下的任何类型；如匹配java.lang.String、java.lang.annotation.Annotation|&nbsp;
|java.lang.*ing  | 匹配任何java.lang包下的以ing结尾的类型|&nbsp;
|java.lang.Number+ |  匹配java.lang包下的任何Number的自类型；如匹配java.lang.Integer，也匹配java.math.BigInteger|&nbsp;

## 实现手法

### 环境
Spring中AOP代理由Spring的IOC容器负责生成、管理，其依赖关系也由IOC容器负责管理，要在Spring 中使用AOP，还需要加入这两个jar包 
1、`aopalliance.jar `
2、`aspectjweaver.jar`

### Spring中 AOP中的两种代理

1. Java动态代理
默认使用 。可以为任何接口实例创建代理了
2. CGLIB
当需要代理的类不是代理接口的时候，Spring会切换为使用CGLIB代理，也可强制使用CGLIB

### Spring AOP的使用步骤
1. 定义具体业务逻辑模块（目标对象） 
2. 定义切面（即实现通知逻辑） 
3. 实现切面逻辑

## 两种方式的例子

两种方式，基于Schema或@AspectJ。

### 一. 基于Schema的Spring AOP

#### 第一步、定义具体业务模块（目标对象）

两个业务模块都是基于接口

TestAOPDaoImpl .java

```java
public class TestAOPDaoImpl implements TestAOPDao{

    @Override
    public void addUser() {
        System.out.println("添加成功");
    }
}
```

TestAOPServiceImpl.java

```java
public class TestAOPServiceImpl implements TestAOPService{

    @Autowired
    private TestAOPDao testAOPDao;

    @Override
    public void addUser() {
        testAOPDao.addUser();
    }
}
```

#### 第二步和第三步、 定义切面（即实现通知逻辑）

**aop创建代理后会返回一个 `连接点JointPoint`**，然后在通知中可以**通过**该连接点实现我们的切面逻辑

**日志切面**

```
public class LogAdivice{

    public void myBeforeAdivice(JoinPoint joinPoint){
        String classname = joinPoint.getTarget().getClass().getSimpleName();
        String methodname = joinPoint.getSignature().getName();
        System.out.println(classname + " ——前置通知——" + methodname);
    }

    public void myAfterAdivice(JoinPoint joinPoint){
        String classname = joinPoint.getTarget().getClass().getSimpleName();
        String methodname = joinPoint.getSignature().getName();
        System.out.println(classname + " ——后置通知——" + methodname);
    }

   //环绕通知将决定要不要执行连接点
    public void myAroundAdivice(ProceedingJoinPoint point) throws Throwable{
        System.out.println("环绕通知，执行代码前");
        //选择执行
        point.proceed();
        System.out.println("环绕通知，执行代码后");
    }
}

```

**时间切面：**

```java
public class TimeAdvice {

    public void timeBefore(){
        System.out.println("beforeTime = " + System.currentTimeMillis());
    }

    public void timeAfter(){
        System.out.println("afterTime = " + System.currentTimeMillis());
    }
}
```

**在applicationContext中配置切面：**

```
<context:annotation-config/>
    <bean id="testAOPDao" class="com.ssh.dao.impl.TestAOPDaoImpl"/>
    <bean id="testAOPService" class="com.ssh.service.impl.TestAOPServiceImpl"/>
    <bean id="logAdivice" class="com.ssh.adivice.LogAdivice"/>
    <bean id="timeAdvice" class="com.ssh.adivice.TimeAdvice"/>

    <aop:config>
       <!-- 配置一个切面 -->
       <aop:aspect id="logaop" ref="logAdivice" order="2">
           <!-- 定义切入点，表示对service的所有方法都进行拦截 -->
           <aop:pointcut expression="execution(* com.ssh.service.TestAOPService.*(..))" id="testpointcut"/>
           <!-- 定义前置通知 -->
           <aop:before method="myBeforeAdivice" pointcut-ref="testpointcut"/>
           <!-- 定义后置通知 -->
           <aop:after-returning method="myAfterAdivice" pointcut-ref="testpointcut"/>
           <!-- 定义环绕通知 -->
           <aop:around method="myAroundAdivice" pointcut-ref="testpointcut"/>
       </aop:aspect>

       <!-- 定义另一个切面 -->
       <aop:aspect id="timeaop" ref="timeAdvice" order="1">
           <!-- 定义切入点，表示对service的所有方法都进行拦截 -->
           <aop:pointcut expression="execution(* com.ssh.service.TestAOPService.*(..))" id="testpointcut"/>
           <!-- 定义前置通知 -->
           <aop:before method="timeBefore" pointcut-ref="testpointcut"/>
           <!-- 定义后置通知 -->
           <aop:after-returning method="timeAfter" pointcut-ref="testpointcut"/>
       </aop:aspect>
    </aop:config>
```

当有多个切面时，Spring默认是按照切面定义的顺序来执行，也可以通过order属性来配置切面的执行属性，order=1 早于 order=2执行

### 测试

```
public class AOPTest {
    public static void main(String[] args) {
        ApplicationContext context = new 
                            ClassPathXmlApplicationContext("applicationContext.xml");
        TestAOPService service = (TestAOPService) context.getBean("testAOPService");
        service.addUser();
    }
}
```

### 二. 基于@AspectJ注解的AOP实现

启用@AsjectJ支持

在applicationContext.xml中配置下面一句:

`<aop:aspectj-autoproxy />`


#### **第一步、定义具体业务逻辑模块（目标对象）**

第一步和基于Schema的一样

TestAOPDaoImpl .java

```
public class TestAOPDaoImpl implements TestAOPDao{

    @Override
    public void addUser() {
        System.out.println("添加成功");
    }
}
```

TestAOPServiceImpl.java

```
public class TestAOPServiceImpl implements TestAOPService{

    @Autowired
    private TestAOPDao testAOPDao;

    @Override
    public void addUser() {
        testAOPDao.addUser();
    }
}
```

#### 第二步和第三步 定义切面（即实现通知逻辑）

重点是**定义切入点**

```
@Aspect
public class LogAdivice{

    //定义一个方法作为切入点id
    @Pointcut("execution(* com.ssh.service.TestAOPService.*(..))")
    private void allMethod(){}

    @Before("allMethod()")
    public void myBeforeAdivice(JoinPoint joinPoint){
        String classname = joinPoint.getTarget().getClass().getSimpleName();
        String methodname = joinPoint.getSignature().getName();
        System.out.println(classname + " ——前置通知——" + methodname);
    }

    @AfterReturning("allMethod()")
    public void myAfterAdivice(JoinPoint joinPoint){
        String classname = joinPoint.getTarget().getClass().getSimpleName();
        String methodname = joinPoint.getSignature().getName();
        System.out.println(classname + " ——后置通知——" + methodname);
    }

  //环绕通知将决定要不要执行连接点
    @Around("allMethod()")
    public void myAroundAdivice(ProceedingJoinPoint point) throws Throwable{
        System.out.println("环绕通知，执行代码前");
        //执行
        point.proceed();
        System.out.println("环绕通知，执行代码后");
    }
}
```

**在applicationContext的配置：**

```
<!-- 打开自动扫描(隐式打开注解管理器） -->
    <!-- <context:component-scan base-package="com.ssh"/> -->
    <context:annotation-config/>
    <bean id="testAOPDao" class="com.ssh.dao.impl.TestAOPDaoImpl"/>
    <bean id="testAOPService" class="com.ssh.service.impl.TestAOPServiceImpl"/>
    <bean id="logAdivice" class="com.ssh.adivice.LogAdivice"/>
    <bean id="timeAdvice" class="com.ssh.adivice.TimeAdvice"/>

    <!-- 打开aop注解管理器 -->
    <aop:aspectj-autoproxy/>
```

**参考文章在各个小结中有链接**
