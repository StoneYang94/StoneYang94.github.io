---
title: 类Instagram项目第二节
date: 2018-07-03 00:05:29
tags:
- python
- 项目
- Flask
categories: 项目
---
用python语言开发类似instagram网站的项目，写博客以整理思路形成系统的认知，加深印象和方便日后复习。
如发现问题，欢迎随时留言讨论。
本文主要介绍 装饰器，Flask框架。并且有学习时运行的截图。

<!-- more -->

# 一. 装饰器

请参考：我的博客[python装饰器](https://www.jianshu.com/p/18239508fe4c) 

通俗不严谨解释：
把函数名称当作参数和返回值，在内部调用，控制调用前后的操作逻辑


# 二. Flask

请参考：我的博客[Flask入门](https://www.jianshu.com/p/f314abea0f3c) 

## 流程

![流程](http://p7vxw6hv7.bkt.clouddn.com/18-6-27/12426781.jpg)

## 一个最简易的网页

```
#-*- encoding=UTF-8 -*-

from flask import Flask 

app = Flask(__name__)    

@app.route('/')   
def index():
    return 'hello'

if __name__ == '__main__':
    app.run(debug=True)   
```

![](http://p7vxw6hv7.bkt.clouddn.com/18-6-27/36003819.jpg)

至此，一个简单的网页就运行起来了，下面就是针对这个网站进行一些细节操作，逐步完善

## 优化

### 多映射
- 意义
避免因后期升级导致旧的连接无法使用
- 语法
在上面添加装饰器，使得可以同时接受多个路径的映射

```
@app.route('/') #指定根路径的回调
@app.route('/index/')
def index():
    return 'hello'
```

![](http://p7vxw6hv7.bkt.clouddn.com/18-6-27/15514573.jpg)

### 参数变量

语法
通过url传递参数

```
@app.route('/profile/<uid>/')
def profile(uid):
    return 'profile : ' + uid
```
![](http://p7vxw6hv7.bkt.clouddn.com/18-6-27/26716533.jpg)


### 错误
指定传递参数的类型，如果传递的参数类型不符合，会直接回404 

```
@app.route('/profileInt/<int:uid>/')
def profileInt(uid):
    return 'profileInt : ' + str(uid)
```

![类型正确](http://p7vxw6hv7.bkt.clouddn.com/18-6-28/86768399.jpg)

![类型错误](http://p7vxw6hv7.bkt.clouddn.com/18-6-28/80544928.jpg)

# 三. 模板(template)
请参考：我的博客[Jinja2模板](https://www.jianshu.com/p/a1103d3eeb0c) 

## 课上的例子
1. 导入`render_template`
2. 在主程序所在目录下创建templates文件夹
3. 把模板文件放到这里面

视频里的模板文件是profile.html

```
<html>
<body>
{# 这个是Jinja2模板语法，这个是注释 #}
uid={{uid}}<br>     {# 这是获取变量 #}

{% for i in range(0,5): %}
    {{loop.index}}, number:{{i}}<br>
{% endfor %}

{# 可以理解定义了一个带参数的宏 #}
{% macro render_color(color) %}
    <div>This is color {{color}}</div>  {{caller()}}
{% endmacro %}

{% for color in colors: %}
<br>
    {% call render_color(color) %}  {# 调用 #}
        render_color_demo<br>
    {% endcall %}
{% endfor %}
</body>
</html>
```

```
#-*- encoding=UTF-8 -*-

from flask import Flask, render_template #导入模块

app = Flask(__name__)   #创建对象

@app.route('/') #指定根路径的回调
@app.route('/index/')
def index():
    return 'hello'

@app.route('/profile/<uid>/')
def profile(uid):
    #return 'profile : ' + uid
    colors = ('blue', 'red', 'green')
    return render_template('profile.html', uid=uid, colors=colors)

@app.route('/profileInt/<int:uid>/')
def profileInt(uid):
    return 'profileInt : ' + str(uid)

if __name__ == '__main__':
    app.run(debug=True) # debug,开启debug模式
```
![](http://p7vxw6hv7.bkt.clouddn.com/18-6-30/79559274.jpg)

## 404模板--`from flask import Flask, render_template, request`

```
404的模板是not_found.html, 也要放在templates里面
添加request模块，获取当前请求的url

```

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Not Found HeiHei</title>
</head>
<body>
    {{request}}
</body>
</html>
```

```
from flask import Flask, render_template, request

@app.errorhandler(404)
def not_found(error):
    return render_template('not_found.html', request=request.url)
```

![自己写404模板](http://p7vxw6hv7.bkt.clouddn.com/18-6-30/38073641.jpg)

## 重定向---`from flask import Flask, render_template, request, redirect `

重定向就是将**网页自动转向重定向**，分为——301永久性重定向和302临时性重定向。
- 301：新网址完全继承旧网址，旧网址的排名等完全清零
- 302：对旧网址没有影响，但新网址不会有排名

```
from flask import Flask, render_template, request, redirect 

@app.route('/newpath')
def newpath():
    return 'newpath'

@app.route('/redirct/<int:number>')
def redirctPage(number):
    return redirect('/newpath',code=number)
```

![![007-redirct.jpg-134.7kB][9]](http://upload-images.jianshu.io/upload_images/11861611-9bb055092def7bc7?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


参考文章
[python初级项目课（二）](https://blog.csdn.net/a312024054/article/details/51960551)
牛客网初级项目（无连接，去网站购买学习）