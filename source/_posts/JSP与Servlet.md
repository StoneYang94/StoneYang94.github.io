---
title: JSP与Servlet
date: 2018-07-09 19:29:48
tags: 
- JSP
- Servlet
categories: Web
---
在 MVC 架构模式中，就 JSP 和 Servlet 而言，C 通常由 Servlet 充当，V 通常由 JSP 来充当。

<!-- more -->

# JSP/Servlet 工作原理

## Servlet
- Servlet `没有 main 方法，不能够独立的运行`，它的运行需要容器的支持，Tomcat 是最常用的 JSP/Servlet 容器。
- Servlet 运行在 Servlet 容器中，并由容器管理从创建到销毁的整个过程。

## Servlet 的生命周期

(1) 加载和实例化
Servlet 容器装载和实例化一个 Servlet。创建出该 Servlet 类的一个实例。
(2) 初始化---`init()`
在 Servlet 实例化完成之后，容器负责调用该 Servlet 实例的 init() 方法，在处理用户请求之前，来做一些额外的初始化工作。
(3) 处理请求---`service()`
当 Servlet 容器接收到一个 Servlet 请求时，便运行与之对应的 Servlet 实例的 service() 方法，service() 方法再派遣运行与请求相对应的 
doXX(doGet，doPost) 方法来处理用户请求。
(4) 销毁---`destory()`
当 Servlet 容器决定将一个 Servlet 从服务器中移除时 ( 如 Servlet 文件被更新 )，便调用该 Servlet 实例的 destroy() 方法，在销毁该 Servlet 实例之前，来做一些其他的工作。
其中，(1)(2)(4) 在 Servlet 的整个生命周期中只会被执行一次。

## Servlet 的工作原理

![Servlet 的工作原理](http://upload-images.jianshu.io/upload_images/11861611-992487a8cc6a877a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


当客户端浏览器向服务器请求一个 Servlet 时
1. 服务器收到该请求后，首先到容器中检索与请求匹配的 Servlet 实例是否已经存在
-  若不存在则 
    1. Servlet 容器负责`加载并实例化`出该类 Servlet
的一个实例对象
    2. 接着容器框架负责调用该实例的` init() `方法来对实例做一些初始化工作
    3. 然后Servlet 容器运行该实例的 `service() `方法
- 若 Servlet 实例已经存在，则
     容器框架直接调用该实例的` service()` 方法
2. service() 方法在运行时，自动派遣运行与用户请求相对应的` doXX() `方法来响应用户发起的请求

通常，每个 Servlet 类在容器中只存在一个实例(Servlet默认是线程不安全的)，每当请求到来时，则分配一条线程来处理该请求。

## JSP 工作原理

![ JSP 工作原理](http://upload-images.jianshu.io/upload_images/11861611-8dbe30b6e8fc4ba1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


当客户端浏览器向服务器请求一个 JSP 页面时
1. 服务器收到该请求后，首先检查所请求的这个JSP 文件内容 ( 代码 ) 是否已经被更新，或者是否是 JSP 文件创建后的第一次被访问
  - 如果是
    1. 这个 JSP 文件就会在服务器端的 JSP 引擎作用下转化为一个` Servlet 类`的 Java 源代码文件
    2. 这个 Servlet 类会在 Java 编译器的作用下被编译成一个`字节码文件`，并装载到 jvm 解释执行。
    3. 剩下的就等同于 Servlet 的处理过程了。
  - 如果不是
那么它的处理过程也等同于一个 Servlet 的处理过程。即直接由服务器检索出与之对应的 Servlet 实例来处理。

## JSP 转换Servlet 类的时机
JSP 文件不是在服务器启动的时候转换成 Servlet 类的。而是在被客户端访问的时候才可能发生转换的 ( 如 JSP 文件内容没有被更新等，就不再发生 Servlet 转换 )。
就 Tomcat 而言，打开目录 `%Tomcat%/work/%工程文件目录%`，然后会看到里面有 3个子目录：`org/apache/jsp`，若没有这 3 个目录，说明项目的 JSP 文件还没有被访问过，打开进到 jsp 目录下，会看到一些 *_jsp.java 和 *_jsp.class 文件，这就是 JSP 文件被转换成Servlet 类的源文件和字节码文件了。

## Servlet 与 JSP区别联系

### 联系
- JSP 本质是一个 Servlet，它的运行也需要容器的支持
- 在 JSP 和 Servlet 文件中都可以编写 Java 和 HTML 代码

### 区别
- Servlet 更加偏向于逻辑的控制
- JSP 更加偏向于页面视图的展现
- 在 MVC 架构模式中，就 JSP 和 Servlet 而言，C 通常由 Servlet 充当，V 通常由 JSP 来充当。

参考文章
[JSP/Servlet 工作原理](http://www.blogjava.net/fancydeepin/archive/2013/09/30/fan_servlet.html)
