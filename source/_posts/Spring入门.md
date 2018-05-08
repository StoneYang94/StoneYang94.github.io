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
- Spring是一个开源框架，最初为了解决企业应用开发的复杂性而创建的，但现在已经不止应用于企业应用。
- 是一个轻量级的控制反转（IOC）和面向切面（AOP）的容器框架。
  - 从**大小与开销**两方面而言Spring都是轻量的
  - 通过控制反转（IOC）的技术达到**松耦合**的目的
  - 提供了面向切面编程(AOP)的丰富支持，允许通过**分离**应用的业务逻辑与系统级服务进行内聚性的开发。
  - 包含并管理应用对象的配置和生命周期，这个意义上是一种**容器。**
  - 将简单的组件配置、组合成为复杂的应用，这个意义上是**框架**
### 为什么是Spring
- 在Spring上开发应用简单
- 在Spring上开发应用方便
- 在Spring上开发应用快捷
- **Spring带来了复杂JavaEE开发的春天**
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
![Spring框架](http://upload-images.jianshu.io/upload_images/11861611-6c924d3183ec90b0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 框架
#### 什么是框架
- 维基百科：软件框架，通常是指为了实现某个业界标准或者完成特定基本任务的软件组件**规范**，也指为了实现某个软件组织规范时，提供规范所要求之基础功能的软件产品。
- 通俗的说，框架就是制定一套规范或者规则（思想），大家（程序员）在该规范或者规程（思想）下工作。用现实生活中的比喻就是：使用别人搭好的舞台，你来做表演。
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
* 用于沟通的中介物的抽象化。
* 实体把自己提供给外界的一种抽象化说明，用以由内部操作**分离**出外部沟通方式，使其能被修改内部而不影响外界其他实体与其交互的方式。
* 对应Java接口即声明，声明了哪些方法对外提供的。
* 在Java8中，接口可以拥有方法体。
### 面向接口编程
- ***结构设计中，分清层次及调用关系，每层只向外（上层）提供一组功能接口，各层间仅依赖接口而非实现类***
- 接口实现的**变动不影响**各层间的调用，这一点在公共服务中尤为重要
- “面向接口编程”中的“接口”是用于**隐藏**具体实现和实现多态性的组件 
## 什么是IOC
- 站在过程的角度看问题
IOC：控制反转，控制权的转移，应用程序本身不负责依赖对象的创建和维护，而是由外部容器负责创建和维护
- 站在主体的角度看问题
DI（依赖注入）是其中一种实现方式
2004年，Martin Fowler探讨了同一个问题，既然IoC是控制反转，那么 到底是“哪些方面被控制反转了呢？”，经过详细的分析和论证后，他得出了答案：“**获得依赖对象的过程**被反转了”。控制被反转之后，获得依赖对象的过程由自身管理变成了由IOC容器的主动注入。于是，他给“控制反转” 取了一个更合适的名字“依赖注入（Dependency Injection）”。他的这个答案，实际上给出了实现IoC的方法：注入。**所谓依赖注入，就是由IoC容器 在运行期间，动态地将某种依赖关系注入到对象之中。**
* 目的：**创建对象并且组装对象之间的关系**
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
IOC容器中bean的唯一标识
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
- Before

```xml
<aop:aspect id="beforeExample" ref="aBean">

    <aop:before
        pointcut-ref="dataAccessOperation"
        method="doAccessCheck"/>

    ...

</aop:aspect>
```

```xml
<aop:aspect id="beforeExample" ref="aBean">

    <aop:before
        pointcut="execution(* com.xyz.myapp.dao..(..))"
        method="doAccessCheck"/>

    ...

</aop:aspect>
```

- After returning advice

```xml
<aop:aspect id="afterReturnExample" ref="aBean">

    <aop:after-returning
        pointcut-ref="dataAccessOperation"
        method="doAccessCheck"/>

    ...

</aop:aspect>
```

```xml
<aop:aspect id="afterReturningExample" ref="aBean">

    <aop:after-returning
        pointcut-ref="dataAccessOperation"
        returing="retVal"
        method="doAccessCheck"/>

    ...

</aop:aspect>
```

- After throwing advice

```xml
<aop:aspect id="afterThrowingExample" ref="aBean">

    <aop:after-throwing
        pointcut-ref="dataAccessOperation"
        method="doAccessCheck"/>

    ...

</aop:aspect>
```

使用throwing属性来指定可被传递的异常的参数名称

```xml
<aop:aspect id="afterThrowingExample" ref="aBean">

    <aop:after-throwing
        pointcut-ref="dataAccessOperation"
        throwing="dataAccessEx"
        method="doAccessCheck"/>

    ...

</aop:aspect>
```

- After(finally) advice

```xml
<aop:aspect id="afterFinallyExample" ref="aBean">

    <aop:after
        pointcut-ref="dataAccessOperation"
        method="doAccessCheck"/>

    ...

</aop:aspect>
```

## Advice应用（下）
- Around advice
通知方法的第一个参数必须是ProceedingJoinPoint类型

```xml
<aop:aspect id="aroundExample" ref="aBean">

    <aop:after
        pointcut-ref="businessService"
        method="doBasicProfiling"/>

    ...

</aop:aspect>
```

```java
public Object doBasicProfiling(ProceedingJoinPoint pjp) throws Throwable{
    //start stopwatch
    Object retVal = pjp.proceed();
    //stop stopwatch
    return retVal;
}
```
##  Introductions应用
- 允许一个切面声明一个实现指定接口的通知对象且提供一个接口实现类**来代表**这些对象
- 由<aop:aspect>中的<aop:declare-parents>元素声明，该元素用于声明所匹配的类型拥有一个新的parent（因此得名）
- schema-defaulted aspects只支持singleton models

```xml
<aop:aspect id="usageTrackerAspect" ref="usageTracking">

    <aop:declare-parents
        types-matching="com.xzy.myapp.service.*+"
        implements-interface="com.xyz.myapp.service.tracking.UsageTracked"
        default-impl="com.xyz.myapp.service.tracking.DefaultUsageTracked"/>

    <aop:before
        pointcut="com.xyz.myapp.SystemArchitecture.businessService()
        and this(usageTracked)"
        method="recordUsage"/>  

</aop:aspect>
```

```java
public void recordUsage(UsageTracked usageTracked){
    usageTracked.incrementUseCount();
}
```

```java
UsageTracked usageTracked = (UsageTracked) context.getBean("myService");
```

## Advisors
- advisor就像一个小的自包含的方面，**只有一个**advice
- 切面自身通过一个bean表示，并且必须实现advice接口，同时， advisor也可以很好的利用AspectJ的切入点表达式
- Spring通过配置文件中<aop:advisor>元素支持advisor 。实际使用中，大多数情况下它会和transaction advice配合使用
- 为了自定义一个advisor的优先级以便让advice可以有序，可以使用order属性来定义advisor的顺序

```xml
<aop:config>

    <aop:pointcut id="businessService"
        expression="execution(* com.xyz.myapp.service..(..))"/>

    <aop:advisor
        pointcut-ref="businessService"
        advice-ref="tx-advice"/>

</aop:config>

<tx:advice id="tx-advice">
    <tx:attribute>
        <tx:method name="*" propagation="REQUIRED"/>
    </tx:attribute>
</tx:advice>
```

# Spring AOP的API介绍
## Spring AOP API的Pointcut、advice概念及应用
### Spring AOP API
- 这是Spring1.2历史用法，当时（V4.0）仍然支持
- 这是SpringAOP**基础**，不得不了解
- 现在的用法也是**基于**历史的，只是更简便了
### Pointcut
实现之一：NameMatchMethodPointcut，根据**方法名字**进行匹配
成员变量：mappedNames，匹配的方法名集合

```xml
<bean id="pointcutBean" class="org.springframework.aop.support.NameMatchMetchMethodPointcut">
    <property name="mappedNames">
        <list>
            <value>sa*</value>
        </list>
    </property>
</bean>
```

### Before advice
- 一个简单的通知类型
- 只是在进入方法之前被调用，**不需要**MethodInvocation对象
- 前置通知可以在连接点执行之前插入自定义行为，但**不能改变**返回值

```java
public interface MethedBeforeAdvice extends BeforeAdvice{
    void before(Method m,Object[] args,Object target) throws Throwable;
}
```

```java
public class CountingBeforeAdvice implements MethodBeforeAdvice{

    private int count;

    public void before(Method m,Object[] args,Object target) throws Throwable{
        ++count;
    }

    public int getCount(){
        return count;
    }
}
```

### Throws advice
- 如果连接点抛出异常，throws advice在连接点返回后被调用
- 如果throws-advice的方法抛出异常，那么它将覆盖原有异常
- 接口org.springframework.aop.ThrowsAdvice不包含任何方法， 仅仅是一个申明，实现类需要实现下面类似的方法：

  - void afterThrowing([Method,args,target],ThrowableSubclass);
  - public void afterThrowing(Exception ex);
  - public void afterThrowing(RemoteException ex);
  - public void afterThrowing(Method method,Object[] args,Object target,Exception ex);
  - public void afterThrowing(Methdo method,Object[] args,Object target,ServletException ex);
### After Returning advice
- 后置通知**必须**实现org.springframework.aop.AfterReturningAdvcie接口

```java
public class CountingAfterReturningAdvice implements AfterReturningAdvice{
    private int count;
    public void afterReturing(Object returnValue,Method m,Object[] args, Object target) throws Throwable{
        ++count;
    }
    public int getCount(){
        return count;
    }
}
```

- 可以访问返回值（但不能进行修改）、被调用的方法、方法的参数和目标
- 如果抛出异常，将会抛出拦截器链，替代返回值
### Interception around advcie
- Spring的切入点模型使得切入点可以**独立于advice重用**，以针对不同的advice可以使用想同的切入点
### Introduction advice
#### 相关
- Spring把引入通知作为一种特殊的**拦截器**
- 需要IntroductionAdvisor和IntroductionInterceptor
- **仅适用于类**，不能和任何切入点一起使用

```java
public interface IntroductionInterceptor extends MethodInterceptor{
    boolean implementsInterface(Class intf);
}
```

```java
public interface IntroductionAdvisor extends Advisor,IntroductionInfo{

    ClassFilter getClassFilter();

    void validateInterfaces() throws IllegalArgumentException;
}

public interface IntroductionInfo{

    Class[] getInterfaces();
}
```

#### 一个Spring test suite的例子
- 如果调用lock()方法，希望所有的setter方法抛出LockedException异常（如果物体不可变，AOP典型例子）

```java
public interface  Lockable{
    void lock();
    void unlock();
    boolean locked();
}
```

- 需要一个完成繁重任务的IntroductionInterceptor，这种情况下，可以使用org.springframework.aop.support.DelegatingIntroductionInterceptor

```java
public class LockMixin extends DelegatingIntroductionInterceptor implements Lockable{
    private boolean locked;
    public void lock(){
        this.locked = true;
    }
    public void unlock(){
        this.locked = false;
    }
    public boolean locked(){
        return this.locked;
    }
    public Object invoke(MethodInvocation invocation) throws Throwable {
        if(locked() && invocation.getMethod().getName().indexOf("set") == 0){
            throw new LockedException();
        }
        return super.invoke(invocation);
    }
}
```
- introduction advisor比较简单，持有独立的LockMixin实例
### Advisor API in Spring
- Advisor是**仅包含一个**切入点表达式关联的单个通知的方面
- 除了introductions，advisor**可用于**任何通知
- org.springframework.aop.support.DefaultPointcutAdvisor是最常用的advisor类， 它可以与MethodInterceptor，BeforeAdvice或者ThrowsAdvice一起使用
- 它可以混合在Spring同一个AOP代理的advisor和advice
## ProxyFactoryBean及相关内容（上）
### 基本方法
- 创建Spring AOP代理的基本方法是使用org.springframework.aop.framework.ProxyFactoryBean
### 好处
- 这可以完全控制切入点和通知（advice）以及他们的顺序
- 使用ProxyFactoryBean或者其它IoC相关类来创建AOP代理的最重要好处是通知和切入点也可以**由IoC来管理**
### JDK还是CGLib
- 被代理类没有实现任何接口，使用CGLIB代理，否者JDK代理
- 通过设置proxyTargetClass为true，可以强制使用CGLIB
- 如果目标类实现一个（或多个）接口，那么创建代理的类型将依赖ProxyFactoryBean的配置
- 如果ProxyFactoryBean的proxyInterfaces属性被设置为一个或者多个全限定接口名，基于JDK的代理将被创建
- 如果ProxyFactoryBean的proxyInterfaces属性没有被设置，但是目标类实现了一个（或者更多）接口，那么ProxyFactoryBean将自动检测到这个目标类已经实现了至少一个接口， 创建一个基于JDK的代理。
## ProxyFactoryBean及相关内容（下）
### Proxying classes
- 前面的例子如果没有使用Person接口，这种情况下Spring会使用CGLIB代理，而不是JDK动态代理
- 如果想，可以在任何情况下使用CGLIB，即使有接口
- CGLIB代理的工作原理是在运行时生成目标类的子类，Spring配置这个生成的 
子类委托方法调用到原来的目标
- 子类是用来实现Decorator模式，织入通知
- CGLIB的代理**对用户是透明**的，需要注意：
  - final方法不能被通知，因为它们不能被覆盖
  - 不用把CGLIB添加到classpath中，在Spring3.2中，CGLIB被重新包装并包含在 Spring核心的JAR（即基于CGLIB的AOP就像JDK动态代理一样“开箱即用”）
### 使用global advisors
用*做通配，匹配所有拦截器加入通知链

```xml
<bean id="proxy" class="org.springframework.aop.framework.ProxyFactoryBean">
    <property name="target" ref="service"/>
    <property name="interceptorNames">
        <list>
            <value>global*</value>
        </list>
    </property>
</bean>

<bean id="global_debug" class="org.springframework.aop.interceptor.DebugInterceptor" />
<bean id="global_performance" class="org.springframework.aop.interceptor.PerformanceMonitorInterceptor" />
```

### 简化的proxy定义
使用父子bean定义，以及内部bean定义，可能会带来更清洁和更简洁的代理定义（抽象属性标记父bean定义为抽象的这样它不能被实例化）

```xml
<bean id="txProxyTemplate" abstract="true" 
    class="org.springframework.transaction.interceptor.TransactionProxyFactoryBean">
    <property name="transactionManager" ref="transactionManager" />
    <property name="transactionAttributes">
        <props>
            <prop key="*">PROPAGATION_REQUIRED</prop>
        </props>
    </property>
</bean>
```

### 使用”auto-proxy“
- Spring也允许使用”自动代理“的bean定义，它可以自动代理选定的bean， 
这是建立在Spring的”bean post processor“功能基础上的（在加载bean 
的时候就可以修改）
- BeanNameAutoProxyCreator

```xml
<bean class="org.springframework.aop.framework.autoproxy.BeanNameAutoProxyCreator">
    <property name="beanNames" value="jdk*,onlyJdk" />
    <property name="interceptorNames">
        <list>
            <value>myInterceptor</value>
        </list>
    </property>
</bean>
```

- DefaultAdvisorAutoProxyCreator，当前IoC容器中自动应用，不用显示声明引用advisor的bean定义

```xml
<bean class="org.springframework.aop.framework.autoproxy.DefaultAdisorAutoProxyCreator" />

<bean class="org.springframework.transaction.interceptor.TransactionAttributeSourceAdvisor">
    <property name="transacationInterceptor" ref="transactionInterceptor"/>
</bean>

<bean id="customAdvisor" class="com.mycompany.MyAdvisor" />

<bean id="businessObject1" class="com.mycompany.BusinessObject1">
    <!-- Properties omitted -->
</bean>

<bean id="businessObject2" class="com.mycompany.BusinessObject2"/>
```

# 第7章 Spring对AspectJ的支持
## AspectJ介绍及Pointcut注解应用
### AspectJ介绍
- @AspectJ的风格类似纯java注解的普通java类
- Spring可以使用AspectJ来做**切入点解析**
- AOP的运行时仍旧是纯的Spring AOP，对AspectJ的编译器或者织入**无依赖性**
### Spring中配置@AspectJ
- 对@AspectJ支持可以使用XML或者Java风格的配置
- 确保AspectJ的aspectjweaver.jar库包含在应用程序（版本1.6.8）的classpath中

```java
@Configuration
@EnableAspectJAutoProxy
public class AppConfig{
}
```

```xml
<aop:aspectj-autoproxy/>
```

### AspectJ使用
- @AspectJ切面使用@Aspect注解配置，拥有@Aspect的任何bean将被Spring**自动识别并应用**
- 用@Aspect注解的类**可以有**方法和字段，他们也可能包括切入点（pointcut），通知（Advice）和引入（introduction）声明
- @Aspect注解是**不能够通过类路径**自动检测发现的，所以需要配合使用@Component注释或者在xml配置bean
- 一个类中的@Aspect注解标识它为一个切面，并且将自己从自动代理中排除

```xml
<bean id="myAspect" class="org.xyz.NotVeryUsefulAspect">
</bean>
```

```java
package org.xyz;
import org.aspectj.lang.annotation.Aspect;

@Aspect
public class NotVeryUsefulAspect{
}
```

### pointcut
- 定义
  - 一个切入点通过一个普通的方法定义来提供，
  - 并且切入点表达式使用@Pointcut注解,
  - 方法返回类型必须为void

- eg.
定义一个名为‘anyOldTransfer’，这个切点将匹配任何名为”transfer“的方法执行@Pointcut("execution(* transfer(..))")//the pointcut expression

```java
@Pointcut("execution(* transfer(..))")//the pointcut expression
private void anyOldTransfer(){}//the pointcut signature
```

### 组合pointcut
- 切入点表达式可以通过&&、||和!进行组合，也可以通过名字引入切入点表达式
- 通过组合，可以建立更加复杂的切入点表达式

```java
@Pointcut("execution(public * (..))")
private void anyPublicOperation(){}

@Pointcut("within(com.xyz.someapp.trading...)")
private void inTrading(){}

@Pointcut("anyPublicOperation() && inTrading()")
private void tradingOperation(){}
```

### 定义良好的pointcuts
- AspectJ是**编译期**的AOP
- **检查**代码并匹配连接点与切入点的代价是**昂贵**的
- 一个好的切入点应该包括以下几点 
  - 选择**特定类型**的连接点，如：execution，get，set，call，handler
  - 确定**连接点范围**，如：within，withincode
  - 匹配**上下文信息**，如：this，target，@annotation
## Advice定义及实例
### Before advice
在执行com.imooc.aop.aspectj包下以Biz结尾的类的所有方法时，匹配Advice。

```java
@Componet
@Aspect
public class MoocAspect{

    @Before("execution(* com.imooc.aop.aspect.biz.*Biz.*(..))")
    public void before(){
        //...
    }
}
```

### After returning advice

```java
@Aspect
public class AfterReturningExample{

    @AfterReturning("com.xyz.myapp.SystemArchitecture.dataAccessOperation()")
    public void doAccessCheck(){
        //...
    }
}
```

有时候需要在通知体内得到返回的实际值，可以使用@AfterReturning的 "returning="绑定返回值的形式

```java
@Aspect
public class AfterReturningExample{

    @AfterReturning(pointcut="com.xyz.myapp.SystemArchitecture.dataAccessOperation()",
                    returning="retVal")
    public void doAccessCheck(Object retVal){
        //...
    }               
}
```

### After(finally) advice
最终通知**必须**准备处理正常和异常**两种返回情况**，它通常用于释放资源。

```java
@Aspect
public class AfterFinallyExample{

    @After("com.xyz.myapp.SystemArchitecture.dataAccessOperation()")
    public void doReleaseLock(){
        //...
    }

}
```

### Around advice
- 环绕通知使用@Around注解来声明，通知方法第一个参数必须是ProceedingJoinPoint类型
- 在通知内部调用ProceedingJoinPoint的proceed()方法会导致执行真正的方法，传入一个Object[]对象，数组中的值将被作为参数传递给方法

```java 
@Aspect
public class AroundExample{

    @Around("com.xyz.myapp.SystemArchitecture.businessService()")
    public Object doBasicProfiling() throws Throwable{
        //start stopwatch
        Object retVal = pjp.proceed();
        //stop stopwatch
        return retVal;
    }
}
```

##  Advice扩展
### 给advice传递参数
#### Advice的参数及泛型
Spring AOP可以处理泛型类的声明和使用方法
#### Advice参数名称
- 通知和切入点注解有一个额外的”argNames“属性，它可以用来指定所注解的方法的参数名
- 如果第一个参数是JoinPoint，ProceedingJoinPoint，JoinPoint.StaticPart，那么可以忽略它
### Introductions
- 允许一个切面声明一个通知对象实现指定接口，并且提供一个接口实现类来代表这些对象
- introduction使用@DeclareParents进行注解，这个注解用来定义匹配的类型拥有一个新的parent
- 例如：给定一个接口UsageTracked，并且该接口拥有DefaultUsageTracked的实现，接下来的切面声明了所有的service接口的实现都实现了UsageTracked接口
### 切面实例化模型
这是一个高级主题
- ”perthis“切面通过指定@Aspect注解perthis子句实现
- 每个独立的service对象执行时都会创建一个切面实例
- service对象的每个方法在第一次执行的时候创建切面实例，切面在service对象失效的同时失效。



