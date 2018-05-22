---
title: Spring事务管理入门
date: 2018-05-22 20:58:17
tags: Spring事务管理
categories: Spring 
---
说明：本文主要内容来自[慕课网](http://www.imooc.com/)。配合[视频](https://www.imooc.com/learn/478)食用口味更佳。
主要是顺着已经学习的视频顺序总结一遍，以深化理解和方便日后复习。
<!-- more -->
# 概念简析
## 事务
逻辑的一组操作，要么一起成功，要么一起失败。
## 事务的特性 ACID
- 原子性：事务是一个不可分割的工作单位。事务中的操作要么一起成功，要么一起失败。
- 一致性：事务前后数据的完整性必须保持一致。
- 隔离性：多个用户并发访问数据库时，一个用户的事务不能被其他用户的事务干扰。可以通过设置隔离级别来实现。
- 持久性：一个事务一旦被提交，那么数据库中数据的改变是永久性的。**即使数据库发生故障**也不该对其有影响。
# spring中事务管理的api
- PlatformTransactionManager：事务管理器
包含了事务的**提交、回滚**等管理。
- TransactionDefinition：事务定义信息
包含了事务的**隔离级别、传播、超时信息**等。
- TransactionStatus：事务具体运行状态
包含了事务是否有保存点、是不是新事务等**状态信息**。
## PlatformTransactionManager
是interface，有一些具体的实现类，比如datasourceTransactionManager。
***spring为不同的持久化框架提供了不同的PlatformTransactionManager。***以最常用的举例。

|框架|	事务|
|:---:|:---:|
|spring jdbc或ibatis、mybatis|org.springframework.jdbc.datasource.DataSourceTransactionManager|
|Hibernate|org.springframework.orm.hibernate3.HibernateTransactionManager|

## TransactionDefinition 
### 隔离级别
- 隔离级别相关的常量，以ISOLATION开头。 
- 数据库在查询数据时,由于种种原因会发生以下三种问题.
  - 脏读：一个事务读取了另一个事务改写但是还没提交的数据。如果这些数据被回滚，则读到的数据是无效的。
  - 不可重复读：在同一个事务中，多次读取同一个数据返回的结果不同。
  - 幻读：一个事务读取了几行数据后，另一个事务插入了一些记录。在后来的查询中，第一个事务就会发现一些原来不存在的记录。

- 事务的隔离级别： 就是为了解决以上问题设定的事务相关的属性

|隔离级别	|含义|
|---|---|
|DEFAULT|	使用后端数据库默认的隔离级别（spring中得选择项）
|READ_UNCOMMITED	|允许读取还没提交的已改变了的数据，可能导致脏读、幻读、不可重复读。
|READ_COMMITED	|允许在并发事务已经提交后读取，可防止脏读。但不可防止幻读和不可重复读。
|REPEATABLE_READ	|对相同字段的多次读取，结果是一致的，除非数据被事务本身改变。可防止脏读、不可重复读，但不可防止幻读。
|SERIALIZABLE	|完全服从ACID的隔离级别，可防止脏读、幻读、不可重复读。这在所有隔离级别中是最慢的，因为它是通过完全锁定在事务中涉及的数据表来完成的。是不可能出现并发访问的情况。

- 默认隔离级别举例：
mysql：REPEATABLE_READ
oracle：READ_COMMITED
Spring中默认的隔离级别就是使用的底层数据库的隔离级别
### 传播行为
- 传播行为相关的常量，以PROPAGATION开头。
- 解决**业务层方法之间的相互调用**的问题。
- web层-->业务层-->持久层
假设持久层有两个dao，dao1，dao2。业务层有两个service，service1和service2。

```java
Dao1{
xxx(){}
}

Dao2{
yyy(){}
}

Service1{
    aaa(){
        dao1.xxx();
        dao2.yyy();
    }
}
 
Service2{
    bbb();
}
```

事务是加在业务层的。若此时业务复杂，需要调用service1和service2的方法来共同完成业务功能。而此时service1和service2都有自己的事务管理，那么事务到底使用service1里的还是service2里的呢？此时就用到了业务的传播行为。

|事务的传播行为|	说明|
|---|---|
|PROPAGATION_REQUIRED	|支持当前事务，如果不存在，就新建一个。
|PROPAGATION_SUPPORTS	|支持当前事务，如果不存在，就不使用事务。
|PROPAGTION_MANDATORY	|支持当前事务，如果不存在，就抛出异常。
|PROPAGATION_REQUIRES_NEW	|如果有事务存在，挂起当前事务，创建 一个新的事务。
|PROPAGATION_NOT_SUPPORTED	|如果有事务存在，挂起当前事务。以非事务的方式进行。
|PROPAGATION_NEVER	|如果有事务存在，抛出异常。以非事务的方式进行。
|PROPAGATION_NESTED|	如果有事务存在，则嵌套事务执行。
 

- 重点记忆这三种：PROPAGATION_REQUIRED，PROPAGATION_REQUIRES_NEW，PROPAGATION_NESTED。
  - PROPAGATION_REQUIRED：保证service1的aaa()和service2的bbb()**在**同一个事务里。
  - PROPAGATION_REQUIRES_NEW：保证service1的aaa()和service2的bbb()**不在**同一个事务里。
  - PROPAGATION_NESTED：service1的aaa()执行完后可以设置一个保存点，如果service2的bbb()执行时出了问题，可以选择回滚到保存点，也可以选择回滚到最初点。是一种**嵌套**事务。
## TransactionStatus
提供一组方法来获取、设置事务的一些状态信息。比如isCompleted()，isRollBackOnly()。
## 三个接口直接的关系
Spring中DepaltformTransactionManager根据TransactionDefinition中设置的事务隔离级别和传播行为,来管理事务，将信息保存到TransactionStatus中。
# Spring的编程式事务管理
需手动编写代码，在需要用到事务的方法中编写代码（一般不使用）
1. 配置事务管理器platformTransactionManager。
对于jdbc 需要datasourceTransactionManager 其中必须定义数据源属性dataSource，jdbc中是c3p0的连接池
2. 事务管理的**模板**(注意和sql模板不同) transactionTemplate 事务管理模板,其属性就是上面对应的事务管理器。
3. 在需要事务管理的代码处使用**内部匿名类**来执行运行sql的代码，这样将所有的操作后台的sql都会放在同一个事务中。
# Spring的声明式事务管理
1. 首先还是要配置事务管理器
2. 配置事务管理具体方法
- 经典ProxyFactoryBean(使用动态代理的技术,用法已经渐渐废弃)
需要配置TransactionProxyFactoryBean 属性包括target(标注哪个Bean需要事务管理) 和 事务管理器(上面配置的),在bean的配置中通过property来配置事务的传播行为和隔离级别。(只能对一个目标进行配置,就是那个target,不好用)
- 使用aop来进行事务管理
使用基于AspectJ的Spring AOP来管理
xml配置:
配置通知  ``` <tx:advice> ``` 
配置切面  ``` <aop:aspect> ``` ,在切面中配置通知和切点。
- 注解配置:
@Transactional即可配置;对需要的类配置,同时也可配置相关的隔离级别和传播行为属性。

参考文章
http://www.cnblogs.com/lyh421/p/6726319.html



