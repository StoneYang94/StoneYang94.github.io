---
title: 类Instagram项目第三节
date: 2018-07-05 21:46:40
tags:
- python
- 项目
- Flask
categories: 项目
---
用python语言开发类似instagram网站的项目，写博客以整理思路形成系统的认知，加深印象和方便日后复习。
如发现问题，欢迎随时留言讨论。
> 数据库交互以及flask-sqlalchemy扩展使用
主要进行项目结构搭建，数据库的交互，基本功能和首页的开发。

本文主要介绍数据库交互以及flask-sqlalchemy扩展使用。主要进行项目结构搭建，数据库的交互，基本功能和首页的开发。

<!-- more -->

# flask-sqlalchemy初识

## 创建SQLAlchemy对象

一般对于只有一个FLASK的应用，你需要做的事情就是
1. 创建 Flask 应用
`app = Flask(__name__)`
2. 加载配置
`app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:////tmp/test.db'`
3. 创建SQLAlchemy对象时候**把 Flask 应用传递给它作为参数**
`db = SQLAlchemy(app)`

- 一旦创建，这个对象就包含`sqlalchemy`和`sqlalchemy.orm`中的所有函数和助手。
- 此外它还提供一个名为`Model`的类，用于声明模型时**继承的父类**

```python
from flask import Flask
from flask.ext.sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:////tmp/test.db'
db = SQLAlchemy(app)  #把Flask应用传递给它作为参数

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True)
    email = db.Column(db.String(120), unique=True)

    def __init__(self, username, email):
        self.username = username
        self.email = email

    def __repr__(self):
        return '<User %r>' % self.username
```

## 创建数据库---`db.create_all()`
为了创建初始数据库，只需要
1. 从交互式 Python shell 中导入`db `对象
2. 调用`SQLAlchemy.create_all()`方法来**创建表和数据库**

```python
from yourapplication import db
db.create_all()
```

## 插入数据---`调用MyClass的构造函数`
向数据库插入数据分为三个步骤:

1. 创建 Python `对象`
2. 把它添加到`会话`
3. `提交`会话

```python
from yourapplication import User
admin = User('admin', 'admin@example.com')
guest = User('guest', 'guest@example.com')
```

## 提交---`db.session.add() + db.session.commit()`
但是它们还没有真正地写入到数据库中，因此让我们来确保它们已经写入到数据库中:

```python
db.session.add(admin)
db.session.add(guest)
db.session.commit()
```

## 访问数据库---`MyClass.query.all() | MyClass.query.filter_by()`

```python
users = User.query.all()
```

[<User u'admin'>, <User u'guest'>]

```python
admin = User.query.filter_by(username='admin').first()
```
<User u'admin'>



---
# pageDetail页面

```
<a title="{{image.user.username}}" href="/profile/{{image.user.id}}">
{{image.user.username}}</a>
```

全局属性title可以在鼠标移到内容位置时候显示一段**工具提示文本**，本例位`image.user.username`

```
@app.route('/image/<int:image_id>/')  #如http://127.0.0.1:5000/image/300/传入浏览器给出的数据
```

# html中herf和src的区别
`href`是Hypertext Reference的缩写，表示超文本引用。用来建立当前元素和文档之间的**链接**。常用的有：link、a。
`src`是source的缩写，src的内容是页面必不可少的一部分，是引入。src指向的内容会**嵌入到**文档中当前标签所在的位置。常用的有：img、script、iframe。



在SQL中使用LIMIT关键词的时候如果没有足够多行的数据则：会返回最多行数