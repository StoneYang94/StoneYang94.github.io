---
title: markdown入门
date: 2018-04-27 16:43:17
tags: markdown 
categories: 基础工具
---

简单介绍了markdown，使用原因和基础语法。

<!-- more -->

# 一 .markdown快速认知
## 整体概览
![左侧为编辑，右侧为预览](http://p7vxw6hv7.bkt.clouddn.com/18-4-28/6529463.jpg)
## 为什么用markdown
Markdown 是一种是一种方便记忆、书写的纯文本轻量级标记语言，不同于平时我们用的字处理软件 Word 或 Pages 有大量的排版、字体设置，它用简洁的语法代替排版。它使我们专心于内容输出而不是排版与设置。
更重要的是github主流支持markdown。
## 具体描述
#### [维基百科](https://zh.wikipedia.org/wiki/Markdown)对markdown定义
> **Markdown**是一种[轻量级标记语言](https://zh.wikipedia.org/wiki/%E8%BD%BB%E9%87%8F%E7%BA%A7%E6%A0%87%E8%AE%B0%E8%AF%AD%E8%A8%80 "轻量级标记语言")，创始人为[约翰·格鲁伯](https://zh.wikipedia.org/wiki/%E7%B4%84%E7%BF%B0%C2%B7%E6%A0%BC%E9%AD%AF%E4%BC%AF "约翰·格鲁伯")（英语：John Gruber）。它允许人们“使用易读易写的纯文本格式编写文档，然后转换成有效的[XHTML](https://zh.wikipedia.org/wiki/XHTML "XHTML")（或者[HTML](https://zh.wikipedia.org/wiki/HTML "HTML")）文档”。
这个语言的目的是希望大家使用“易于阅读、易于撰写的纯文字格式，并选择性的转换成有效的XHTML（或是HTML）”。 其中**最重要的设计是可读性**，也就是说这个语言应该要能直接在字面上的被阅读，而不用被一些格式化指令标记（像是RTF与HTML）。

Markdown 的语法十分简单，常用的标记符号也不超过十个。相对于更为复杂的 HTML 标记语言来说，Markdown 可谓是十分轻量的，用户可以使用这些标记符号以最小的输入代价生成极富表现力的文档，故而易学易用。
# 二.markdown编译器  
> 参考[各个平台选择markdown编译器](http://www.williamlong.info/archives/4319.html)
博主主要在web端使用[简书](https://www.jianshu.com/)这款产品，支持左右两栏的实时预览。是一个很不错的博客平台，每几秒钟便会自动存入一个备份。可以直接从本地拖入照片生成链接，一直在不断优化。作为一个博客平台，需要[注册账号](https://www.jianshu.com/sign_up)后方能进行写作。
# 三.语法
下列图片展示都是左侧为编辑区域，右侧为预览效果。
## 段落
段落的前后要有空行，所谓的空行是指没有文字内容。若想在段内强制换行的方式是使用两个以上空格加上回车（引用中换行省略回车）。
## 分割线
分割线最常使用就是三个或以上*，还可以使用-和_。
## 标题
在一段文字前加 # 号这段文字就是标题，如：
* 写法  "  # 一级标题  "
![共六级标题](http://p7vxw6hv7.bkt.clouddn.com/18-4-29/25028156.jpg)
## 列表
在文字前加上 - 或 * 即可变为无序列表，在文字前加1. 2.  3. 符号要和文字之间加上一个字符的空格。
* 无序列表写法  "  * 标题一 "
* 有序列表写法  "  1. 标题一  "
![列表](http://p7vxw6hv7.bkt.clouddn.com/18-4-30/95130232.jpg)
## 引用
如果你需要引用一小段别处的句子，那么就要用引用的格式，用大于号">"。
* 写法  "  > 以下是引用  "
![引用](http://p7vxw6hv7.bkt.clouddn.com/18-4-30/92796687.jpg)
## 图片与链接
插入链接与插入图片的语法很像，区别在一个 !号。
* 图片    " [name]"+"(url)  "
* 链接     "!"+"(name)"+"[url]"
插入图片的地址需要图床，推荐[极简图床 + chrome 插件 + 七牛空间](https://jiantuku.com/#/)
[配置的具体教程](https://www.jianshu.com/p/44d818f781a7)
## 强调
Markdown 的粗体和斜体也非常简单，用两个 * 包含一段文本就是粗体的语法，用一个 * 包含一段文本就是斜体的语法。
![强调](http://p7vxw6hv7.bkt.clouddn.com/18-4-30/24164005.jpg)
## 代码块
* 代码块
使用三个`(数字1左边的按键)来包含多行代码：
![代码块](http://p7vxw6hv7.bkt.clouddn.com/18-4-30/30477519.jpg)
## 表格
Item     | Value
-------- | ---
Computer | $1600
Phone    | $12
Pipe     | $1
## 详细了解请戳官方文档
[Markdown 语法说明 (简体中文版)](http://wowubuntu.com/markdown/#list)