---
title: HTML入门
date: 2018-06-28 19:13:51
tags:
- HTML
- 前端
categories: 前端
---
博主在使用Flask过程，预在项目中使用中Jinja模板。学习Jinja模板离不开基础的HTML的知识。
本文是学习笔记性质的，主要为了形成对HTML的相对全面的认识和为日后复习备忘。
推荐学习的网站[W3School的HTML教程](http://www.w3school.com.cn/html/index.asp)，本文部分内容和例子来自学习的内容，但对其进行了整理、筛选和补充，并附上了自己的运行截图。

<!-- more -->

# 简介
## 什么是 HTML？
- HTML 超文本标记语言 (Hyper Text Markup Language)是用来描述网页的一种语言。
- HTML 不是一种编程语言，而是一种标记语言 (markup language)， **使用标记标签描述网页。**

## HTML 标签-- <HTML tag>
HTML 标记标签通常被称为 HTML 标签 (HTML tag)，是由**尖括号包围**的关键词，比如 `<html>`
HTML 标签通常是**成对出现**的，比如` <b>` 和 `</b>`，标签对中的第一个标签是开始标签，第二个标签是结束标签

## HTML 文档 = 网页
- HTML 文档包含 HTML 标签和纯文本，Web 浏览器的作用是读取 HTML 文档，并以网页的形式显示出它们。
- 浏览器不会显示 HTML 标签，而是使用标签来解释页面的内容：

```html
<html>
<body>

<h1>我的第一个标题</h1>

<p>我的第一个段落。</p>

</body>
</html>
```

![查看结果](http://upload-images.jianshu.io/upload_images/11861611-89a513f190c17b93.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

解释:
<html> 与 </html> 之间的文本描述网页
<body> 与 </body> 之间的文本是可见的页面内容
<h1> 与 </h1> 之间的文本被显示为标题
<p> 与 </p> 之间的文本被显示为段落

# 常用标签介绍

## 一. 布局

### HTML 标题--<h1>…</h1>
HTML 标题（Heading）是通过 <h1> - <h6> 等标签进行定义的。

例子：

```html
<h1>This is a heading</h1>
<h2>This is a heading</h2>
<h3>This is a heading</h3>
```

![标题](http://upload-images.jianshu.io/upload_images/11861611-0042d201059085c2.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### HTML 段落--<p>…</p>
- 由于HTML文档会忽略空白符，所以要想保证正常的分段换行的话，必须指出哪些文字是属于同一段落的
- HTML 段落是通过` <p>` 标签进行定义的

例子：

```html
<p>This is a paragraph.</p>
<p>This is another paragraph.</p>
```

![段落](http://upload-images.jianshu.io/upload_images/11861611-1c7d7d08f282abda.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 容器--- <div>
HTML的 <div> 元素是可用于组合其他 HTML 元素的容器，它没有特定的含义，浏览器会在其前后显示折行。
- 样式属性
如果与 CSS 一同使用，<div> 元素可用于对大的内容块设置样式属性
- 文档布局
它取代了使用表格定义布局的老式方法
不应该使用 <table> 元素进行文档布，<table> 元素的作用是显示表格化的数据

#### HTML+CSS
- 层叠样式表CSS（Cascading Style Sheets），用来控制HTML里的所有元素如何展现
- 如果与 CSS 一同使用，<div> 元素可用于对大的内容块设置样式属性

```html
<body>

<div id="header">
<h1>City Gallery</h1>
</div>

<div id="nav">
London<br>
Paris<br>
Tokyo<br>
</div>

<div id="section">
<h1>London</h1>
<p>
London is the capital city of England. It is the most populous city in the United Kingdom,
with a metropolitan area of over 13 million inhabitants.
</p>
<p>
Standing on the River Thames, London has been a major settlement for two millennia,
its history going back to its founding by the Romans, who named it Londinium.
</p>
</div>

<div id="footer">
Copyright W3School.com.cn
</div>

</body>
```

![仅有HTML](http://upload-images.jianshu.io/upload_images/11861611-c77e0485744585d4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
<style>
#header {
    background-color:black;
    color:white;
    text-align:center;
    padding:5px;
}
#nav {
    line-height:30px;
    background-color:#eeeeee;
    height:250px;
    width:100px;
    float:left;
    padding:5px; 
}
#section {
    width:350px;
    float:left;
    padding:10px; 
}
#footer {
    background-color:black;
    color:white;
    clear:both;
    text-align:center;
    padding:5px; 
}
</style>
```

![HTML+CSS](http://upload-images.jianshu.io/upload_images/11861611-a7c8b16fbc156e44.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 使用 HTML5 的网站布局
HTML5 提供的新语义元素定义了网页的不同部分：

|HTML5 |语义元素|
|---|---|
|header |定义文档或节的页眉
|nav(navigation)|定义导航链接的容器
|section    |定义文档中的节
|article    |定义独立的自包含文章
|aside  |定义内容之外的内容（比如侧栏）
|footer |定义文档或节的页脚
|details|   定义额外的细节
|summary    |定义 details 元素的标题


```HTML
<body>

<header>
<h1>City Gallery</h1>
</header>

<nav>
London<br>
Paris<br>
Tokyo<br>
</nav>

<section>
<h1>London</h1>
<p>
London is the capital city of England. It is the most populous city in the United Kingdom,
with a metropolitan area of over 13 million inhabitants.
</p>
<p>
Standing on the River Thames, London has been a major settlement for two millennia,
its history going back to its founding by the Romans, who named it Londinium.
</p>
</section>

<footer>
Copyright W3School.com.cn
</footer>

</body>
```

CSS代码也做了细微调整

```
<style>
header {
    background-color:black;
    color:white;
    text-align:center;
    padding:5px; 
}
nav {
    line-height:30px;
    background-color:#eeeeee;
    height:250px;
    width:100px;
    float:left;
    padding:5px; 
}
section {
    width:350px;
    float:left;
    padding:10px; 
}
footer {
    background-color:black;
    color:white;
    clear:both;
    text-align:center;
    padding:5px; 
}
```


### 分隔线---\<hr>
- `<hr>`标签用于在页面上添加横线。
- 可以通过指定`width`和`color`属性来控制横线的长度和颜色。

例子：

```html
<hr width="80%" color="yellow" />
<hr width="60%" color="red" />
```

![分隔线](http://upload-images.jianshu.io/upload_images/11861611-1550c09d000612a4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 切换行---\<br/>
- 希望在不产生一个新段落的情况下进行换行（新行）
- 使用空的段落标记 <p></p> 去插入一个空行是个坏习惯。用 <br /> 标签代替它

例子：

```html
<h1>This is a <br/> heading</h1>
```

![切换行](http://upload-images.jianshu.io/upload_images/11861611-5f56509917142a76.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 注释---`<!-- 注释 -->`
可以将注释插入 HTML 代码中，这样可以提高其可读性，使代码更易被人理解。浏览器会忽略注释，也不会显示它们。

例子：

```html
<h1>This is a heading</h1>
<!-- This is a comment -->
```

![注释](http://upload-images.jianshu.io/upload_images/11861611-a1d031f45df15ed3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 二. 外链

### 链接---<a href="url">desc</a>

HTML 链接是通过 <a> 标签进行定义的。

实例：

```html
<a href="http://www.w3school.com.cn">This is a link</a>
```

![链接](http://upload-images.jianshu.io/upload_images/11861611-a82cff249fe66b5a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在 href （Hypertext Reference）属性中指定链接的地址。

### 图像---<img src="url" width=" " height=" " />

HTML 图像是通过 <img> 标签进行定义的，图像的**名称和尺寸等**是以属性的形式提供的。

- width：宽
- height：高

#### 实例

```html
<img src="http://p7vxw6hv7.bkt.clouddn.com/18-6-27/12426781.jpg"
width="300" height="142" />
```

![图像](http://upload-images.jianshu.io/upload_images/11861611-480a6d7fea24de8d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 替换文本属性---alt="des"
alt 属性用来为图像定义一串预备的可替换的文本。替换文本属性的值是用户定义的。

例子：

```html
<img src="random.gif" alt="Big Boat">
```

![alt](http://upload-images.jianshu.io/upload_images/11861611-22be632a6943949c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在浏览器无法载入图像时，替换文本属性告诉读者她们失去的信息。此时，浏览器将显示这个替代性的文本而不是图像。为页面上的图像都加上替换文本属性是个好习惯，这样有助于更好的显示信息，并且对于那些使用纯文本浏览器的人来说是非常有用的。

# 三. 文本格式化标签

| 标签 | 描述 |
|---|---|
| <b> | 定义粗体文本 |
| <big> | 定义大号字 |
| <em>| 定义着重文字 |
| <i> | 定义斜体字 |
| <small>| 定义小号字 |
| <strong>| 定义加重语气 |
| \<sub> | 定义下标字 |
|\<sup>| 定义上标字 |
| <ins>| 定义插入字 |
| <del> | 定义删除字 |


# 四. 样式---style
当浏览器读到一个样式表，它就会按照这个样式表来对文档进行格式化。有三种方式来插入样式表

## 外部样式表---<head><link rel=" "  type=" "  href=" "></head>
当样式需要被应用到**很多页面**的时候，外部样式表将是理想的选择。使用外部样式表，你就可以通过更改一个文件来改变整个站点的外观。

```html
<head>
<link rel="stylesheet" type="text/css" href="mystyle.css">
</head>
```

## 内部样式表---<head>  <style type
当**单个文件**需要特别样式时，就可以使用内部样式表。你可以在 head 部分通过 `<style> `标签定义内部样式表。

```html
<head>

<style type="text/css">
body {background-color: red}
p {margin-left: 20px}
</style>
</head>
```

![内部样式表](http://upload-images.jianshu.io/upload_images/11861611-924c404ca68b8621.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 内联样式---<p style=
当特殊的样式需要应用到**个别元素**时，就可以使用内联样式。 使用内联样式的方法是在相关的标签中使用样式属性。样式属性可以包含任何 CSS 属性。以下实例显示出如何改变段落的颜色和左外边距。

```html
<p style="color: red; margin-left: 20px">
This is a paragraph
</p>
```

![内联样式](http://upload-images.jianshu.io/upload_images/11861611-40ae0b740051226f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 五. 表格---<table>

## 基本语法---<tr>  <td>data</td>  </tr>
- `<table> `:表格
- `<tr> `:行
- `<td> `:每行被分割为若干单元格
字母 td 指表格数据（table data），即数据单元格的内容。数据单元格可以包含文本、图片、列表、段落、表单、水平线、表格等等。

```html
<table border="1">
<tr>
<td>row 1, cell 1</td>
<td>row 1, cell 2</td>
<td>row 1, cell 3</td>
</tr>
<tr>
<td>row 2, cell 1</td>
<td>row 2, cell 2</td>
<td>row 2, cell 3</td>
</tr>
</table>
```

![表格](http://upload-images.jianshu.io/upload_images/11861611-18112ad8bc7b5c48.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 表格的表头---<th>Heading</th>

` <th>` :表头

大多数浏览器会把表头显示为粗体居中的文本：

```html
<table border="1">
<tr>
<th>Heading</th>
<th>Another Heading</th>`
</tr>
<tr>
<td>row 1, cell 1</td>
<td>row 1, cell 2</td>
</tr>
<tr>
<td>row 2, cell 1</td>
<td>row 2, cell 2</td>
</tr>
</table></pre>
```

![表头](http://upload-images.jianshu.io/upload_images/11861611-3f7ec2602d7b8108.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 表格中的空单元格---<td>\&nbsp</td>

在一些浏览器中，没有内容的表格单元显示得不太好。如果某个单元格是空的（没有内容），浏览器可能无法显示出这个单元格的边框。

为了避免这种情况，在空单元格中添加一个空格占位符，就可以将边框显示出来。

```html
<table border="1">
<tr>
<td>row 1, cell 1</td>
<td>row 1, cell 2</td>
</tr>
<tr>
<td>&nbsp</td>
<td>row 2, cell 2</td>
</tr>
</table></pre>
```

![空单元格](http://upload-images.jianshu.io/upload_images/11861611-dd4784eac6667f95.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 六. 列表

## 无序列表---<ul>…</ul>
无序列表是一个项目的列表，此列项目使用粗体圆点（典型的小黑圆圈）进行标记。
无序列表始于 <ul> 标签。每个列表项始于 <li>。

```html
<ul>
<li>Coffee</li>
<li>Milk</li>
</ul>
```

![无序列表](http://upload-images.jianshu.io/upload_images/11861611-2ac27bedba7256ca.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 有序列表---<ol>…</ol>
同样，有序列表也是一列项目，列表项目使用数字进行标记。
有序列表始于 <ol> 标签。每个列表项始于 <li> 标签。

```html
<ol>
<li>Coffee</li>
<li>Milk</li>
</ol>
```

![有序列表](http://upload-images.jianshu.io/upload_images/11861611-a343a6cf8f0cf8d8.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




## 自定义列表<dl>  <dt>  …</dt>  <dd> …</dd>  </dl>
自定义列表不仅仅是一列项目，而是项目及其注释的组合。
- `<dl>` ：自定义列表开始
- `<dt> `：每个自定义列表项 开始
 - `<dd>`：每个自定义列表项**的定义** 开始

```html
<dl>
<dt>Coffee</dt>
<dd>Black hot drink</dd>
<dt>Milk</dt>
<dd>White cold drink</dd>
</dl>
```

![自定义列表](http://upload-images.jianshu.io/upload_images/11861611-f4e55bbe354df661.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




参考文章
[W3School的HTML教程](http://www.w3school.com.cn/html/index.asp)