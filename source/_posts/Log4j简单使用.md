---
title: Log4j简单使用
date: 2018-07-29 22:36:04
tags: Log4j
categories: Spring 
---
Log4j 是一个使用 Java 语言编写的，可靠、快速、灵活的日志框架（API）

<!-- more -->

# 概述

## 记录日志的优缺点
记录日志是软件开发中的重要一环。编写良好的日志代码能为运行应用提供快速的诊断信息和良好的存储结构，**方便维护**。

记录日志也有其缺点，它会让**应用变慢**。假如输出太详细，可能会导致屏幕闪动。为了减轻这些影响，Log4j 被设计为可靠`的，更快的和可扩展的。

由于日志很少是应用程序关注的焦点，所以 Log4j API 力争做到`简单并易于理解和使用`。

## Log4j 是什么
Log4j 是一个使用 Java 语言编写的，可靠、快速、灵活的日志框架（API），使用 Apache Software License 授权。
它被移植到 C、C++、C#、Perl、Python、Ruby 和 Eiffel 语言中。

Log4j 是高度可配置的，在运行期使用外部的配置文件对其进行配置。它按照优先级别记录日志，并可将日志信息定向输出到各种介质，比如数据库、文件、控制台、Unix Syslog等。

## Log4j 主要由三部分组成：

loggers：负责采集日志信息
appenders：负责将日志信息发布到不同地方
layouts：负责以各种风格格式化日志信息

### Logger 对象---`获取`

最上一层是 Logger 对象，该对象负责
- 获取日志信息，并存储于一个分层的命名空间之中

### Layout 对象---`格式化`

Layout 层提供了用于以各种风格格式化日志信息的对象，在发布日志信息之前，它为 appender 对象提供支持。

Layout 对象对于发布日志信息非常重要，它使日志变得可读、可复用。

### Appender 对象---`分发`

该对象位于分层架构中的较低一层，Appender 对象负责将日志信息发布到不同目的地，比如数据库、文件、控制台、Unix Syslog 等。

![Log4j 框架中的组件](http://p7vxw6hv7.bkt.clouddn.com/18-7-25/4150316.jpg)

# Logging 方法

## 1. 获取 Logger 对象
Logger 类提供了很多方法用来处理日志，Logger 类`不允许初始化一个新的实例`，但提供了两个静态方法用来`获取` Logger 对象：

```java
public static Logger getRootLogger();
public static Logger getLogger(String name);
```

第一个方法返回应用实例没有名字的根日志。

其他有名字的 Logger 对象通过传入日志的名字，调用第二个方法获得。**日志的名字是传入的任何字符串**，通常为**类名或包名**：

```java
static Logger log = Logger.getLogger(Log4jExample.class.getName());
```

## Logging 方法
一旦获取一个有名字的 logger 实例，就可以使用多个方法记录日志。Logger 类拥有如下方法用于打印日志信息。

|方法|描述|
|---|---|
|public void debug(Object message)|使用 Level.DEBUG 级别打印日志
|public void error(Object message)|使用 Level.ERROR 级别打印日志
|public void fatal(Object message)|使用 Level.FATAL 级别打印日志
|public void info(Object message)|使用 Level.INFO 级别打印日志
|public void warn(Object message)|使用 Level.WARN 级别打印日志
|public void trace(Object message)|使用 Level.TRACE 级别打印日志

**所有级别均在 org.apache.Log4j.Level 类中定义**，这些方法使用如下方式调用：

```java
import org.apache.Log4j.Logger;

public class LogClass {
   private static org.apache.Log4j.Logger log = Logger.getLogger(LogClass.class);

   public static void main(String[] args) {

      log.trace("Trace Message!");
      log.debug("Debug Message!");
      log.info("Info Message!");
      log.warn("Warn Message!");
      log.error("Error Message!");
      log.fatal("Fatal Message!");
   }
}
```

编译并运行 LogClass，输出如下：

```
Debug Message!
Info Message!
Warn Message!
Error Message!
Fatal Message!
```

调试信息和`级别`联合使用才更有意义。

# Logging 级别

**org.apache.Log4j.Level 类定义了日志级别**，您可通过继承 Level 类定制自己的级别。

|级别|描述|
|---|---|
|ALL	|所有级别，包括定制级别
|DEBUG	|指明细致的事件信息，对调试应用最有用
|INFO	|指明描述信息，从粗粒度上描述了应用运行过程
|WARN|指明潜在的有害状况
|ERROR	|指明错误事件，但应用可能还能继续运行
|FATAL	|指明非常严重的错误事件，可能会导致应用终止执行
|OFF	|最高级别，用于关闭日志



## 级别是如何工作的？
在一个级别为 q 的 logger 对象中，一个级别为 p 的日志请求在 p >= q 的情况下是开启的。该规则是 Log4j 的核心，它假设级别是有序的。对于标准级别，其顺序为：`ALL < DEBUG < INFO < WARN < ERROR < FATAL < OFF`

## 使用`setLevel(Level.X) `方法设置日志级别

下面的例子展示了如何过滤 DEBUG 和 INFO 级别的日志。改程序使用 logger 对象的 **`setLevel(Level.X) `方法设置期望的日志级别**：

该例子会打印出**除 DEBUG 和 INFO 级别外的所有信息**：

```
import org.apache.Log4j.*;

public class LogClass {
   private static org.apache.Log4j.Logger log = Logger.getLogger(LogClass.class);

   public static void main(String[] args) {
      log.setLevel(Level.WARN);

      log.trace("Trace Message!");
      log.debug("Debug Message!");
      log.info("Info Message!");
      log.warn("Warn Message!");
      log.error("Error Message!");
      log.fatal("Fatal Message!");
   }
}
```

编译并运行 LogClass，会产生如下输出：

```
Warn Message!
Error Message!
Fatal Message!
```

## 使用配置文件设置日志级别

Log4j 提供了基于配置文件设置日志级别的功能，当您需要改变调试级别时，不用再去修改代码了。

下面这个例子和上面那个例子功能一样，不过不用使用 
`setLevel(Level.WARN)` 方法，只需修改配置文件：

```
# Define the root logger with appender file
log = /usr/home/Log4j
Log4j.rootLogger = WARN, FILE

# Define the file appender
Log4j.appender.FILE=org.apache.Log4j.FileAppender
Log4j.appender.FILE.File=${log}/log.out

# Define the layout for file appender
Log4j.appender.FILE.layout=org.apache.Log4j.PatternLayout
Log4j.appender.FILE.layout.conversionPattern=%m%n
```

现在使用如下程序：

```
import org.apache.Log4j.*;

public class LogClass {

   private static org.apache.Log4j.Logger log = Logger.getLogger(LogClass.class);

   public static void main(String[] args) {

      log.trace("Trace Message!");
      log.debug("Debug Message!");
      log.info("Info Message!");
      log.warn("Warn Message!");
      log.error("Error Message!");
      log.fatal("Fatal Message!");
   }
}
```

编译并运行如下程序，会在 /usr/home/Log4j/log.out 文件内生成如下内容：

```
Warn Message!
Error Message!
Fatal Message!
```



参考文章
[极客学院](http://wiki.jikexueyuan.com/project/log4j/logging-methods.html)

