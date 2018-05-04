---
title: Spring入门
date: 2018-05-04 22:33:04
tags: Spring入门 
categories: Spring 
---
说明：本文主要内容来自慕课网。[慕课网](http://www.imooc.com/)
主要是顺着已经学习的视频顺序总结一遍，以深化理解和方便日后复习。
<!-- more -->
# 第一章 概述
## Spring 入门课程简介
本门课程包含的内容
### 什么是框架
Spring简介
IOC（配置、注解）
Bean（配置、注解）
AOP（配置、注解、AspetJ、API）
### 如何学习Spring
掌握用法
深入理解
不断实践
反复总结
再次深入理解与实践
## Spring概况
### Spring是什么
Spring是一个开源框架，为了解决企业应用开发的复杂性而创建的，但现在已经不止应用于企业应用。
是一个轻量级的控制反转（IOC）和面向切面（AOP）的容器框架
* 从**大小与开销**两方面而言Spring都是轻量的
* 通过控制反转（IOC）的技术达到松耦合的目的
* 提供了面向切面编程的丰富支持，允许通过分离应用的业务逻辑与系统级服务进行内聚性的开发。
* 包含并管理应用对象的配置和生命周期，这个意义上是一种**容器。**
* 将简单的组件配置、组合成为复杂的应用，这个意义上是**框架**
### 为什么是Spring
* 在Spring上开发应用简单
* 在Spring上开发应用方便
* 在Spring上开发应用快捷
**Spring带来了复杂JavaEE开发的春天**
### Spring作用
* 容器
* 提供了多种技术的支持
    --JMS
    --MQ支持
    --UnitTest
* AOP（事务管理、日志等）
* 提供了众多方便应用的辅助类（JDBC Template等）
* 对主流应用框架（Hibernate等）提供了良好的支持
### 适用范围
* 构建企业应用（SpringMVC+Spring+Hibernate/MyBatis）
* 单独使用Bean容器（Bean管理）
* 单独使用AOP进行切面处理
* 其他的Spring功能：如：对消息的支持等
* 在互联网中的应用
## Spring框架
### 框架
#### 什么是框架
* 维基百科：软件框架，通常是指为了实现某个业界标准或者完成特定基本任务的软件组件**规范**，也指为了实现某个软件组织规范时，提供规范所要求之基础功能的软件产品。
* 通俗的说，框架就是制定一套规范或者规则（思想），大家（程序员）在该规范或者规程（思想）下工作。用现实生活中的比喻就是：使用别人搭好的舞台，你来做表演。
#### 为什么使用框架
* 软件系统日趋**复杂**
*  **重用度高**，开发效率和质量提高
* 软件设计人员要专注于对领域的了解，使需求分析更充分
* 易于上手、快速解决问题
#### 框架的特点
* 半成品
* 封装了特定的处理流程和控制逻辑
* 成熟的、不断升级的软件
#### 框架与类库的区别 
* 框架一般是**封装了逻辑、高内聚的**，类库则是**松散**的工具组合。
* 框架专注于某一领域，类库则是更通用的。
（）（）（）（）（）（）（）（
# 第二章 Spring IOC 容器
## 接口与面向接口编程
### 接口
* 用于沟通的中介物的抽象化。
* 实体把自己提供给外界的一种抽象化说明，用以由内部操作**分离**出外部沟通方式，使其能被修改内部而不影响外界其他实体与其交互的方式。
* 对应Java接口即声明，声明了哪些方法对外提供的。
* 在Java8中，接口可以拥有方法体。
### 面向接口编程
* 结构设计中，分清层次及调用关系，每层只向外（上层）提供一组 
功能接口，各层间仅依赖接口而非实现类
* 接口实现的**变动不影响**各层间的调用，这一点在公共服务中尤为重要
* “面向接口编程”中的“接口”是用于**隐藏**具体实现和实现多态性的组件 
## 什么是IOC
* 站在过程的角度看问题
IOC：控制反转，控制权的转移，应用本身不负责依赖对象的创建和 
维护，而是由外部容器负责创建和维护
* 站在主体的角度看问题
DI（依赖注入）是其中一种实现方式
2004年，Martin Fowler探讨了同一个问题，既然IoC是控制反转，那么 
到底是“哪些方面被控制反转了呢？”，经过详细的分析和论证后，他得 
出了答案：“**获得依赖对象的过程**被反转了”。控制被反转之后，获得依赖 
对象的过程由自身管理变成了由IOC容器的主动注入。于是，他给“控制反转” 
取了一个更合适的名字“依赖注入（Dependency Injection）”。他的这个 
答案，实际上给出了实现IoC的方法：注入。所谓依赖注入，就是由IoC容器 
在运行期间，动态地将某种依赖关系注入到对象之中。
* 目的：**创建对象并且组装对象之间的关系**
（）（）（）（）（）（
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

    <bean id="oneInterface" class="com.imooc.ioc.interfaces.OneInterfaceImpl"></bean>

 </beans>
```

[Java bean是什么](https://www.zhihu.com/question/19773379)

## Bean容器初始化

### 基础：两个包
* org.springframework.beans
*  org.springframework.context
* BeanFactory提供配置结构和基本功能，加载并初始化Bean
*  ApplicationContext保存了Bean对象，并在Spring中广泛使用
###方式：ApplicationContext
1.  本地文件 -> FileSystemXmlApplicationContext
2.  类路径  -> ClassPathXmlApplicationContext
3. Web应用中依赖servlet或Listener  
***
1. 文件
  FileSystemXmlApplicationContext context = new FileSystemXmlApplicationContext("F:/workspace/appcontext.xml");
2. Classpath
  ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("classpath:spring-context.xml");
3.  Web应用

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
Spring注入是指在启动Spring容器加载bean配置的时候，完成对变量的**赋值行为**。常用的两种注入方式设值注入与构造注入。
### 设值注入
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

### 构造注入
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
## Spring Bean装配之Bean的配置项及作用域
### Bean配置项
- Id
- Class
- Scope
- Constructor argument
- Properties
- Autowiring mode
- lazy-initialization mode
- Initialization/destruction method
### Bean的作用域
- singleton：单例，指一个Bean容器中只存在一份
- prototype：每次请求（每次使用）创建新的实例，destroy方式不生效
- request：每次http请求创建一个实例且仅在当前request内有效
- session：同上，每次http请求创建，当前session内有效
- global session：基于portlet的web中有效（portlet定义了global session），如果在web中，同session
## Spring Bean装配之Bean的生命周期
### 初始化
- 实现org.springframework.beans.factory.InitializingBean接口，覆盖afterPropertiesSet方法
- 配置init-method

```xml
<bean id="exampleInitBean" class="examples.ExampleBean" init-method="init"></bean>
```

```java
public class ExampleBean{
    public void init(){
        //do some initialization work
    }
}
```

```java
public class ExampleInitializingBean implements InitializingBean{ 
    @Override 
    public void afterPropertiesSet() throws Exception{ 
    //do something 
    } 
}
```

## 销毁
- 实现org.springframework.beans.factory.DisposableBean接口，覆盖destory方法
- 配置destory-method

```xml
<bean id="exampleInitBean" class="examples.ExampleBean" destroy-method="cleanup"></bean>
```

```java
public class ExampleBean{
    public void cleanup(){
        //do some destruction work(like releasing pooled connections)
    }
}
```

```java
public class ExampleDisposableBean implements DisposableBean{
    @Override
    public void destroy() throws Exception{
        //do something
    }
}
```

### 配置全局默认初始化、销毁方法

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd" 
        default-init-method="defautInit" default-destroy-method="defaultDestroy">

 </beans>
```

##  Spring Bean装配之Aware接口
Aware
- Spring中提供了一些以Aware结尾的接口，实现了Aware接口的bean在**初始化之后， 可以获取相应资源**
- 通过Aware接口，可以对Spring相应资源进行操作（一定要慎重）
- 为对Spring进行简单的扩展提供了方便的入口

##  Spring Bean装配之自动装配（Autowiring）
Bean的自动装配
- No：不做任何操作。
- byName：根据属性名自动装配。此选项将检查容器并根据名字查找与属性完全一致的bean，并将其与属性自动装配。
- byType：如果容器中存在一个与指定属性类型相同的bean，那么将于该属性自动装配；如果存在多个该类型bean，那么抛出异常，并指出不能使byType方式进行自动装配；如果没有找到相匹配的bean，则什么事都不发生。
- constructor：与byType方式类似，不同之处在于它应用于构造器参数。如果容器中没有找到与构造器参数一致的bean， 那么抛出异常。

## Spring Bean装配之Resource
- 针对于**资源文件**的统一接口
- Resources
  - UrlResources：URL对应的资源，根据一个URL地址即可构建
  - ClassPathResource：获取类路径下的资源文件
  - FileSystemResource：获取文件系统里面的资源
  - ServletContextResource：ServletContext封装的资源，用于访问ServletContext环境下的资源
  - InputStreamResource：针对于输入流封装的资源
  - ByteArrayResource：针对于字节数组封装的资源

# 第四章：Spring Bean装配（下）
## Bean的定义及作用域的注解实现
### Classpath扫描与组件管理
- 从Spring3.0开始，Spring JavaConfig项目提供了很多特性，包括使用 java而不是XML定义bean，比如@Configuration，@Bean，@Import，@DependsOn
- @Component是一个通用注解，可用于任何bean
- @Repository,@Service,@Controller是更具针对性的注解 
  - @Repository通常用于注解DAO类，即持久层
  - @Service通常用于Service类，及服务层
  - @Controller通常用于Controller类，及控制层（MVC）


## Spring Bean装配之Autowired注解说明-1  @Required

- @Require注解适用于bean属性的**setter方法**
- 这个注解仅仅表示，受影响的bean属性**必须**在配置时被填充， 通过在bean定义或者通过自动装配一个明确的属性值

## Spring Bean装配之Autowired注解说明-2  @Autowired
- 可以使用@Autowired注解那些**众所周知**的解析依赖性接口。
比如：BeanFactory，ApplicationContext，Environment，ResourceLoader，ApplicationEventPublisher,and MessageSource

## Spring Bean装配之Autowired注解说明-3  @Qualifier
- 按类型装配可能多个bean实例的情况，可以使用Spring的@Qualifier注解**缩小**范围（或指定唯一）， 也可以用于指定**单独**的构造器参数或方法参数
- 可用于注解**集合**类型变量

## Spring Bean装配之基于Java的容器注解说明——@Bean
- @Bean标识一个用于**配置和初始化**一个由SpringIoC容器管理的新对象的方法， 类似于XML配置文件中的<\bean>
- 可以在Spring的@Conponent注解的类中使用@Bean注解任何方法（仅仅是可以），但通常使用的是@Configuration
##Spring Bean装配之基于Java的容器注解说明——@ImportResource和@Value 
@ImportResource和@Value注解进行资源文件的读取



# 第五章：Spring AOP概念
## AOP基本概念及特点
### 什么是AOP
- AOP：(Aspect Oriented Programming)意为：面向切面编程，通过**预编译**方式和运行期**动态代理**实现程序功能的**统一维护**的一种技术。
- 主要功能是：日志记录，性能统计，安全控制，事务处理，异常处理等
### AOP实现方式
- 预编译 
AspectJ
- 运行期间动态代理（JDK动态代理、CGLib动态代理） 
SpringAOP、JbossAOP
### AOP几个关键概念
|名称|	说明|
|:---:|---|
|切面（Aspect）|	一个关注点的模块化，这个关注点可能会横切多个对象
|连接点（Joinpoint）|	程序执行过程中的某个特定的点
|通知（Advice）	|在切面的某个特定的连接点上执行的动作
|切入点（Pointcut）|	匹配连接点的断言，在AOP中通知和一个切入点表达式关联
|引入（Introduction）|	在不修改类代码的前提下，为类添加新的方法和属性
|目标对象（Target Object）	|被一个或多个切面所通知的对象
|AOP代理（AOP Proxy）	|AOP框架创建的对象，用来实现切面契约（aspect contract）（包括通知方法执行等功能）
|织入（Weaving）	|把切面连接到其它的应用程序类型或者对象上，并创建一个被通知的对象，分为：编译时织入、类加载时织入，执行时织入
### Advice的类型
|名称	|说明
|:---:|---|
|前置通知（Before advcie）|	在某连接点（join point）之前执行的通知，但不能阻止连接点前的执行（除非它抛出一个异常）
|返回后通知（After returning advice）	|在某连接点（join point）正常执行完成后执行的通知
|抛出异常后通知（After throwing advice）|	在方法抛出异常退出时执行的通知
|后通知（After（finally）advice）|	当某个连接点退出的时候执行的通知（不论是正常返回还是异常退出）
|环绕通知（Around Advice）	|包围一个连接点（join point）的通知
### Spring框架中AOP的用途
- 提供了声明式的企业服务，特别是**EJB的替代**服务声明
- 允许用户定义自己的方面，已完成OOP与AOP的互补使用
### Spring的AOP实现
- **纯java实现，无需特殊的编译过程，不需要控制类加载器层次**
- 目前只支持方法执行连接点（通知Spring Bean的方法执行）
- 不是为了提供最完整的AOP实现（尽管它非常强大）；而是侧重于提供一种AOP实现和Spring IoC容器之间的整合，用于帮助解决企业应用中的常见问题
- Spring AOP不会与AspectJ竞争，从而提供综合全面的AOP解决方案
### 有接口和无接口的Spring AOP实现区别
- Spring AOP默认使用标准的JavaSE动态代理作为AOP代理，这使得 
任何接口（或者接口集）都可以被代理
- Spring AOP中也可以使用CGLIB代理（如果一个业务对象并没有实现 
一个接口）
(（）（）（）（）（（）（
## 配置切面aspect
### Schema – based AOP
- Spring所有的切面和通知器都必须放在一个<aop:config>内（可以配置包含多个<aop:config>元素），每一个<aop:config>可以包含 pointcut， advisor和aspect元素（它们**必须按照这个顺序进行声明**）。
- <aop:config>风格的配置大量使用了Spring的自动代理的机制

```xml
<aop:config>
    <aop:aspect id="myAspect" ref="aBean">
        ...
    </aop:aspect>
</aop:config>

<bean id="aBean" class="...">
    ...
</bean>
```

## 配置切入点pointcut
- execution用于**指定**切点

|传入参数| 切点|
|---|---|
|execution(public**(..)) | 切入点为执行所有**public方法**时
|execution(set(..)) |切入点为执行所有**set开始**的方法时
|execution(com.xyz.service.AccountService.(..)) |切入点为执行AccountService类中的所有方法时
|execution(*com.xyz.service..(..)) |切入点为执行com.xyz.service包下的所有方法时
|execution(*com.xyz.service…(..)) |切入点为执行com.xyz.service包及其子包下的所有方法时

- within用于**匹配**指定类型内的方法执行
within(com.xyz.service.*)      (only in Spring AOP)
within(com.xyz.service..*)   (only in Spring AOP) 
- this用于**匹配**当前AOP代理对象类型的执行方法
this(com.xyz.service.AccountService)   (only in Spring AOP) 
- target用于**匹配**当前目标对象类型的执行方法
target(com.xyz.service.AccountService)(only in Spring AOP) 
- args用于**匹配**当前执行的方法传入的参数为指定类型的执行方法
args(java.io.Serializable)(only in Spring AOP) 
## Advice应用（上）
### 

## Advice应用（下）
### 
