---
title: SpringMVC工作原理及组件
date: 2018-07-30 00:05:54
tags: SpringMVC
categories: Spring 
---
# 一、SpringMVC介绍

## 先说Web MVC开发模式
Web MVC模式的核心思想是将**业务逻辑从界面中分离出来**，允许它们单独改变而不会相互影响。

<!-- more -->

### 定义
模型-视图-控制器（MVC）是一个众所周知的以设计界面应用程序为基础的设计模式。它主要通过分离**模型、视图及控制器**在应用程序中的角色`将业务逻辑从界面中解耦`。

![Web MVC](http://p7vxw6hv7.bkt.clouddn.com/18-7-26/62395471.jpg)

## Web MVC各模块作用

### 模型（Model）
- 封装应用数据在视图层展示

## 视图（View）
- 展示数据，不包含任何业务逻辑

## 控制器（Controller）
- 接收来自用户的请求，并调用后台服务（manager或者dao）来处理业务逻辑

## 什么是SpringMVC
SpringMVC是一种基于Java的实现了Web MVC设计模式的请求驱动类型的轻量级Web框架，使用了MVC架构模式的思想，**将web层进行职责解耦**，简化我们日常Web开发。

## SpringMVC是Web MVC的V(View）
SpringMVC和Struts2都属于**表现层的框架**，它是Spring框架的一部分，我们可以从Spring的整体结构中看得出来： 

![SpringMVC属于表现层的框架](http://p7vxw6hv7.bkt.clouddn.com/18-7-18/42119068.jpg)

# 二、SpringMVC运行原理

![SpringMVC运行原理](http://upload-images.jianshu.io/upload_images/11861611-88b5368cb320fb4a?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 流程步骤分析
1. 浏览器发送请求，请求具体发到谁呢？先发到前端控制器，也就是说所有的请求都给发到前端控制器，前端控制器是所有请求的入口，但前端控制器不能处理业务请求，它只是一个**请求的转发**。

2. 谁来处理业务请求呢？**Handler处理器来真正处理业务请求**，那么问题来了，前端控制器如何来找到这个Handler处理器呢？处理器映射器记录的就是请求的url和处理的方法之间的映射关系，这个映射关系是怎么建立起来的呢？就是通过@RequestMapping这个注解来建立起来的，这个映射关系就相当于一个Map(key-value这种形式)，key就是请求的url，value就是处理的Handler。现在，前端控制器拿到这个请求之后，要找到对应的Handler，怎么找呢？就要找处理器映射器，问它请求谁来处理？

3. **处理器映射器会根据你请求的url来找对应的处理器**，找不到就会报错，如果找到之后，这时，它就会返回一个处理器执行链，这个处理器执行链里面除了有Handler之外，还有拦截器(这儿我们可以开发自己的拦截器)，然后返回给前端控制器。

4. 前端控制器依然不能处理这个业务请求，它这时做的还有另外一件事情，因为返回Handler，它也不知道这个Handler是什么类型，因为在spring mvc中Handler除了可以是注解形式的之外，其实还可以是非注解形式的(非注解形式我们一般不用)，前端控制器并不知道这个Handler到底是什么类型的，那就没办法执行它，那总得找个东西执行，这时它就会把这个事交给另外一个组件来处理，这个组件就叫处理器适配器，这个**处理器适配器就是来适配不同类型的Handler**。它就会根据你不同类型的Handler来选择不同类型的适配器来执行它。

5. 假如当前Handler是注解形式的，那么它就会选择注解形式的处理器适配器来执行这个Handler。**Handler就执行了，也就是说我们Controller类中的那个方法就执行了，方法执行之后，里面的业务就处理了**。

6. 业务处理之后，最后返回一个ModelAndView。处理器适配器拿到这个结果是没有用的，它的作用就是执行这个Handler，把这个Handler执行完之后，它的事就做完了。

7. 做完之后，拿到这个返回结果，那么它会原封不动地把这个返回结果扔给前端控制器，这时处理器适配器的事就做完了。

8. 前端控制器拿到这个ModelAndView，它还是没有办法处理，它还是不能返回html，这时它要找到相应的jsp，因为ModelAndView即包含模型又包含视图，这个视图指定我们要用谁来渲染这个数据。我们要渲染数据，这时它就要找一个视图解析器来解析这个视图，由于这个**视图也有很多种(我们最常见的视图是jsp，除了jsp，其实还有其他的，比如说还可以是报表，还可以是pdf，还可以是freemaker等)**，它会找不同的视图解析器来处理。因为现在我们最常用的视图是jsp，所以它就找到jsp对应的视图解析器。

9. 找到这个视图解析器，它来把这个视图解析，**解析完了之后它会返回一个View对象**。

10. 最后我们再调用这个视图解析器的渲染视图这个过程，渲染视图这个过程其实就是对于我们的jsp来说，就是把这个数据渲染成html。

11. 最终渲染成html之后，就响应给用户。

## 流程步骤总结

- 1. （接收）`用户发送请求——>DispatcherServlet`
前端控制器收到请求后**自己不进行处理，而是委托给其他的解析器进行处理**，作为统一访问点，进行全局的流程控制
- 2、3. （映射）`DispatcherServlet——>HandlerMapping`
HandlerMapping将会把请求映射为HandlerExecutionChain对象（包含一个Handler处理器（页面控制器）对象 + 多个HandlerInterceptor拦截器）对象，通过这种策略模式，很容易**添加**新的映射策略
- 4. （适配）`DispatcherServlet——>HandlerAdapter`
HandlerAdapter将会把处理器包装为适配器，从而支持多种类型的处理器，即适配器设计模式的应用，从而很容易**支持**很多类型的处理器
- 5. （调用）`HandlerAdapter——>处理器功能处理方法的调用`
HandlerAdapter将会根据适配的结果调用真正的处理器的功能处理方法，完成功能处理；并返回一个ModelAndView对象（包含模型数据、逻辑视图名）
- 6. （解析）`ModelAndView的逻辑视图名——> ViewResolver`
ViewResolver将把逻辑视图名解析为具体的View，通过这种策略模式，很容易更换其他视图技术
- 7. （渲染）`View——>渲染`
View会根据传进来的Model模型数据进行渲染，此处的Model实际是一个Map数据结构，因此很容易支持其他视图技术
- 8. （返回）`返回控制权给DispatcherServlet`
由DispatcherServlet返回响应给用户，到此一个流程结束。

# 三、框架组件

## DispatcherServlet：前端控制器
用户请求到达前端控制器，它就相当于mvc模式中的c，**DispatcherServlet是整个流程控制的中心**，由它调用其它组件处理用户的请求，DispatcherServlet的存在降低了组件之间的耦合性。

## HandlerMapping：处理器映射器
HandlerMapping负责**根据用户请求找到处理器（Handler）**，springmvc提供了不同的映射器实现不同的映射方式，例如：配置文件方式，实现接口方式，注解方式等。

## HandlAdapter：处理器适配器
通过HandlerAdapter**对处理器进行执行**，这是适配器模式的应用，通过扩展适配器可以对更多类型的处理器进行执行。

## Handler：处理器
在DispatcherServlet的控制下Handler**对具体的用户请求进行处理。**
由于Handler`涉及到具体的用户业务请求`，所以一般情况需要程序员根据业务需求开发Handler。

## View Resolver：视图解析器
View Resolver负责将处理结果生成View视图，View Resolver首先根据逻辑视图名解析成物理视图名即具体的页面地址，再生成View视图对象，最后对View进行渲染将处理结果通过页面展示给用户。

## View：视图
springmvc框架提供了很多的View视图类型的支持，包括：jstlView、freemarkerView、pdfView等。我们最常用的视图就是`jsp`

一般情况下需要通过页面标签或页面模版技术将模型数据通过页面展示给用户，`需要由程序员根据业务需求开发具体的页面`。

说明：在springmvc的各个组件中，处理器映射器、处理器适配器、视图解析器称为springmvc的**三大组件**。

需要用户（程序员）开发的组件有handler、view

# 四、springmvc与struts2不同

## 1. 底层实现机制 match
struts2:filter
springmvc:servlet

## 2. 运行效率
struts2:底层是Servlet,参数基于属性封装,如果配置单例,会出现线程安全问题,所以配置多例
springmvc:底层是Servlet,单例

## 3. 参数封装
struts2:基于属性封装
springmvc:基于方法进行封装


参考文章
[SpringMVC框架](https://www.jianshu.com/p/25a78698d7fa)
[springMVC入门（springMVC笔记）](https://www.jianshu.com/p/21b143ad235b)