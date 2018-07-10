---
title: JSP入门
date: 2018-07-10 23:46:50
tags: JSP
categories: Web
---
# JSP概述

- JSP( Java Server Page)，它和servlet技术一样，都是SUN公司定义的一种用于开发动态web资源的技术。
- JSP与Servlet一样，是在服务器端执行的。
- 它实现了Html语法中的java扩展（以 <%  %>形式）。`JSP= html+java`
- 其根本是一个简化的Servlet

<!-- more -->

# JSP的原理

翻译-> 编译 -> 执行
`demo.jsp `翻译成` _demo_jsp.java`
`_demo_jsp.java` 编译` _demo_jsp.class`

# JSP与Servlet分工

- Servlet：控制器
重点编写java代码逻辑（获取表单数据，处理业务逻辑，分发转向）。
- JSP: 代码显示模板
重点在于显示数据

# JSP的基本语法

## 1. JSP模板元素
- JSP 页面中的`HTML内容`称之为JSP模板元素。
- JSP 模板元素定义了网页的骨架，即定义了页面的结构和外观。

## 2. JSP 脚本片段---`<% code;  %>`
脚本程序可以包含任意量的Java语句、变量、方法或表达式，只要它们在脚本语言中是有效的。

- 只能出现java代码，不能出现其他模板元素，JSP引进在翻译JSP页面中，会将JSP脚步片段中的Java代码**原封不动**的放倒servlet中的_jspService方法中
- JSP 脚本片段的Java代码必须严格遵循java语法 每行语句结束要有（;）.
- 多个脚本之间的变量可以相互访问

**语法**

```
<%
    //多行java代码
%>
```

**例子**

```
<% 
    out.write("hello"); 
    out.write("142");
%>
```

##  3. JSP表达式---`<%= 变量表达式 %>`
一个JSP表达式中包含的脚本语言表达式，**先被转化成String，然后插入到表达式出现的地方**。

- 用于将程序的数据**输出**到客户端
- jsp表达式中的变量和表达式后面**不能有(;)**

**语法**

```jsp
<%= 表达式 %>
```

**例子**

```
<%= 2+8 %>
```

## 4. JSP声明---`<%!  declaration %>`
一个声明语句可以声明一个或多个变量、方法，供后面的Java代码使用。在JSP文件中，您必须**先声明**这些变量和方法然后才能使用它们。

**语法**

```
<%! declaration; [ declaration; ]+ ... %>
```

**例子**

```
<%! int i = 0; %> 
<%! int a, b, c; %> 
<%! Circle a = new Circle(2.0); %> 
```

## 5. JSP注释---`<%-- 被注释的内容 --%> `

|语法|描述|
|---|---|
|`<%-- 注释 --%>`|JSP注释，注释内容不会被发送至浏览器甚至不会被编译|
|`<!-- 注释 -->	`|HTML注释，通过浏览器查看网页源代码时可以看见注释内容|

# JSP 指令---`<%@ directive attribute="value" %>`

## JSP指令定义
是为JSP引擎而设计的，他们**并不直接产生任何输出，而只是告诉浏览器如何处理JSP页面的其余部分**。


|指令|描述|
|---|---|
|<%@ page ... %>|	定义页面的依赖属性，如脚本语言、error页面、缓存需求
|<%@ include ... %>	|包含其他文件
|<%@ taglib ... %>	|引入标签库的定义，可以是自定义标签

**语法**

```
<%@ 指令 属性名1=“值1” 属性名2=“值2”  属性名3=“值3”>
或者
<%@ 指令 属性名1=“值1”%>
<%@ 指令 属性名2=“值2”%>
<%@ 指令 属性名3=“值3”%>
```

**例子**

```
<%@ page contentType="text/html; charset=UTF-8"  pageEncoding="UTF-8"%>
```
### 1. page
- 作用
用于定义JSP页面的各种属性，page指令最好是放在整个jsp页面的起始位置。
- 属性

|属性|定义|默认
|---|---|---|
|language="java"	|指定JSPContainer用什么语言来编译，目前只支持Java语言|默认为JAVA
|extends=”className”	|定义此JSP网页产生的Servlet是继承哪个|&nbsp;
|import=”importList”|	定义此JSP网页要使用哪些Java API|&nbsp;
|session = "true false"|	决定此页面是否使用session对象|默认为ture
|buffer=”none size in kb”	|决定输出流(Input stream)是否又缓冲区|默认为8kb
|isThreadSafe=”true false”|	是否支持线程|默认为true
|errorPage=”url”	|如果此页发生异常，网页会重新指向一个url|&nbsp;
|isErrorPage=”true false”	|表示此页面是否为错误处理页面|默认为false
|contentType=”text/html;charset=gb2312”	|表示MIME类型和JSP的编码方式|&nbsp;
|pageEncoding=”ISO-8859-1”	|编码方式|&nbsp;
|isELLgnored=”true false”	|表示是否在此JSP页面中EL表达式。true则忽略，反之false则支持|默认为false


例

```
<%@ page language="java" contentType="text/html; charset=UTF-8"
pageEncoding="UTF-8"  errorPage="error.jsp" %>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>
<%
    int i= 10/0;
%>
</body>
</html>
```


error.jsp:
```
<%@ page language="java" contentType="text/html; charset=UTF-8"
pageEncoding="UTF-8" isErrorPage="true"%>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>错误页面</title>
</head>
<body>
errorMesg:
<% out.write(exception.getMessage());%>
</body>
</html>
```

```
web.xml配置error 页面
 <error-page>
  <error-code>500</error-code>
  <location>/error.jsp</location>
</error-page>
```

### 2. include

静态包含---`<% include file=" "%>`
把其他资源包含到当前页面

```
<% include file="/header.jsp"%>
<% include file="/footer.jsp"%>
```

动态包含 ---`<jsp:include  ></jsp:include>`

```
<jsp:include page="/foorer"></jsp:include>
```

- 两者的区别：翻译的时间段不同
  - 静态在翻译时就把两个文件合并
  - 动态不会合并文件，当代码执行到include时，才包含另外一个文件内容
- 能用静态就不用动态

例

header.jsp 

```
<%@ page language="java" contentType="text/html; charset=UTF-8"
pageEncoding="UTF-8"%>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>

我是是头部
index.jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
pageEncoding="UTF-8"%>
<%@ include file="/header.jsp" %>
我是内容
<%@ include file="/footer.jsp" %>
footer.jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
pageEncoding="UTF-8"%>
我是底部

</body>
</html>
```

### 3. taglib

作用：在jsp页面中导入jstl标签库。替换jsp中的java代码片段

```
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
```

# jsp 动作---`<jsp:action_name attribute="value" />`

## 常见动作

|动作|作用|
|---|---|
|<jsp:include >| 动态包含
|<jsp:forward> |请求转发
|<jsp:param> |设置请求参数
|<jsp:useBean> |创建一个对象
|<jsp:setProperty>| 给指定的对象属性赋值
|<jsp:getProperty> |取出指定对象的属性值

例

```
<jsp:useBean id="stu1" class="com.itheima.entity.Student"></jsp:useBean>
     <jsp:setProperty property="name" name="stu1" value="jerry"/>
     <jsp:getProperty property="name" name="stu1"/>
     
 <!-- http://localhost:8080/test.jsp?name=123 -->
 <jsp:forward page="/7.jsp">
    <jsp:param value="123" name="name"/>
    <jsp:param value="333" name="pwd"/>
 </jsp:forward>
```

## 常见属性
所有的动作要素都有两个属性：id属性和scope属性。
- `id`属性：
id属性是动作元素的唯一标识，可以在JSP页面中引用。动作元素创建的id值可以通过PageContext来调用。
- `scope`属性：
该属性用于识别动作元素的生命周期。 id属性和scope属性有直接关系，scope属性定义了相关联id对象的寿命。 
scope属性有四个可能的值： (a) page, (b)request, (c)session, 和 (d) application。

## JSP内置对象

JSP支持九个自动定义的变量：

|对象|描述|
|---|---|
|request	|HttpServletRequest类的实例|
|response|	HttpServletResponse类的实例|
|out|	PrintWriter类的实例，用于把结果输出至网页上|
|session	|HttpSession类的实例|
|application	|ServletContext类的实例，与应用上下文有关|
|config	|ServletConfig类的实例|
|pageContext|	PageContext类的实例，提供对JSP页面所有对象以及命名空间的访问|
|page	|类似于Java类中的this关键字|
|Exception|Exception类的对象，代表发生错误的JSP页面中对应的异常对象|