---
title: Python装饰器
date: 2018-06-26 19:24:48
tags: Python
categories: Python 
---
- Python 中的函数和 Java、C++不太一样，Python 中的函数可以像普通变量一样当做参数传递给另外一个函数
- 本质上，Python语言的decorator就是一个**返回函数的高阶函数**
- Python与Java装饰器都是让其他函数或类在不需要做任何代码修改的前提下增加额外功能 。但python装饰器直接实现了AOP，作用是Java中的注解；Java中AOP要用Spring实现

<!-- more -->

# 背景

## 问题提出

1. 夏天的短袖到冬天没法为我们防风御寒
2. 我们想到的一个办法就是把短袖改造一下，让它变得更厚更长，这样一来，它到冬天还能为我们防风御寒（功能增强）
3. 但问题是，这个内裤被我们改造成了加厚长袖后，虽然能为我们在冬天防风御寒，但本质上它不再是一件真正的短袖（改变了函数）
4. 我们想不改变短袖的本质，却还在冬天为我们防风御寒
5. 于是聪明的人们发明外套，在不改变短袖的前提下，直接把外套套在了短袖外面。这样短袖还是短袖，套上外套后也能为我们在冬天防风御寒（不改变函数，功能增强）

## python的函数

**函数可以像普通变量一样当做参数传递给另外一个函数**

```python
def test():
    print("this is what I want")

def receive(func):
    print("I receive a function")
    func()

receive(test)
```

![输出](http://upload-images.jianshu.io/upload_images/11861611-774a0d0c8deeb39a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## python函数的强大

装饰器在 Python 使用如此方便都要归因于 Python 的函数能像普通的对象一样能
1. **作为参数**传递给其他函数，可以被赋值给其他变量
2. 可以**作为返回值**
3. 可以**被定义在另外一个函数内**。

# 装饰器

## 意义
用于有切面需求的场景，比如：插入日志、性能测试、事务处理、缓存、权限校验等场景
用装饰器抽离出大量与函数功能本身无关的雷同代码到装饰器中并继续重用

## 定义

- 装饰器本质上是一个 Python 函数或类，它可以**让其他函数或类在不需要做任何代码修改的前提下增加额外功能**
- 装饰器的返回值也是一个函数/类对象

## 例子

### 业务函数

```python
def test():
    print("this is what I want")
```

### 新需求
现在有一个新的需求，希望可以记录下函数的执行日志，于是在代码中添加日志代码(新的需求)：

```python
def test():
    print("call test()")  #新需求
    print("this is what I want")
```

如果函数 `test()、test2()……`等一大组函数也有类似的需求，需要在这些函数里重复写新需求，造成问题：
1. 修改工作量大
2. 造成大量雷同的代码
3. 不利于之后的业务扩展

### 新定义业务无关的函数

- 重新定义一个辅助函数如`log(func)`专门处理日志（业务无关的代码，新需求）
- 日志处理完之后再执行真正的业务代码`test()`

```python
def log(func):
    print("call test()") 
    func()

def test():
    print("this is what I want")

log(test)
```
[图片上传失败...(image-fd6eee-1529993035512)]

### 存在问题
- 上面的修改实现了功能（不改变业务函数，加入新功能）
- 但是调用的时候不再是调用真正的业务函数`test()`，而是调用辅助函数`log()`

这破坏了原有的代码结构， 每次调用都要把原来的业务函数`test()`作为参数传递给辅助函数`log()`

## 简单装饰器

**想要调用业务函数而不是 把业务函数作为参数传给辅助函数，就要用到装饰器**

```python
def log(func):
    def wrapper():
        print("call test()")
        return func()     # 执行传入的函数参数，如test()
    return wrapper     #返回函数对象 wrapper

def test():
    print("this is what I want")

test= log(test) 
# 因为装饰器 use_logging(test) 返回的是函数对象 wrapper
# 这条语句相当于  test = wrapper

test()         # 执行test() 相当于执行 wrapper()
```

辅助函数`log()`就是一个装饰器，它把执行真正业务函数 func 包裹在其中，看起来像 `test()`被` log()`装饰了一样。

这么做可以实现面向切面编程AOP。

## 装饰器进阶

### 一. @ 语法糖

- @ 符号是装饰器的语法糖，它放在函数开始定义的地方
- 这样就可以省略最后一步再次赋值的操作
本例子中`@use_logging`相当于`test= use_logging(test) `

```python
def log(func):
    def wrapper():
        print("call test()")
        return func()
    return wrapper

@log
def test():
    print("this is what I want")

test()
```

![输出](http://upload-images.jianshu.io/upload_images/11861611-a0472235335fec1b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 如此一来，业务函数`test() `不需要做任何修改，只需**在定义的地方加上装饰器，调用的时候不用加任何修饰**
- 如果我们有其他的类似函数，我们可以继续调用装饰器如`@log`来修饰函数，而不用重复修改函数或者增加新的封装

这样，我们就提高了程序的可重复利用性，并增加了程序的可读性。



### 二. 带参数的业务函数

#### \*args、\**kwargs用法

`*args`和`**kwargs`是python中的可变参数。

```python
def foo(*args, **kwargs):
    print 'args = ', args
    print 'kwargs = ', kwargs
    print '---------------------------------------'

if __name__ == '__main__':
    foo(1,2,3,4)
    foo(a=1,b=2,c=3)
    foo(1,2,3,4, a=1,b=2,c=3)
    foo('a', 1, None, a=1, b='2', c=3)
```

输出结果：

![输出](http://upload-images.jianshu.io/upload_images/11861611-35feda32629209fe.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- `*args`表示任何多个无名参数，它是一个tuple
- `**kwargs`表示关键字参数，它是一个dict
- 同时使用`*args`和`**kwargs`时，必须`*args`参数列要在`**kwargs`前

#### 有限个参数
当业务函数` test() `需要参数，比如 `test(a,b)`：

```python
def test(a,b):
    print("a+b = %d" % (a+b))
```

我们可以在定义 `wrapper() `函数的时候指定参数：

```python
def log(func):
    def wrapper(a,b):
        print("call test(%d，%d)" %(a,b))
        return func(a,b)
    return wrapper
```

这样业务函数` test(name)`定义的参数就可以定义在`wrapper ()`函数中。

总的代码：

```python
# -*- coding: UTF-8 -*- 
def log(func):
    def wrapper(a,b):
        print("call test(%d，%d)" %(a,b))
        return func(a,b)
    return wrapper

@log
def test(a,b):
    print("sum = %d" % (a+b))

test(2,4)
```

![输出](http://upload-images.jianshu.io/upload_images/11861611-36a111de3c8cac75.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 可变参数

当装饰器不知道业务函数到底有多少个参数时，用` *args` 来代替，

如此一来，不管业务函数 `test(…) `定义了多少个参数，都可以完整地传递到 `func `中去

```python
def log(func):
    def wrapper(*args):
        print("call test()" )
        return func(*args)
    return wrapper

@log
def test(a,b,c):
    print("sum = %d" % (a+b+c))

test(2,4,5)
```

[图片上传失败...(image-82a649-1529993035512)]

#### 含关键字的可变参数
如果业务函数 `test(…)`还定义了一些关键字参数，用` **kwargs`来代替：

```python
def test(a,b,c,way=None):
    print("sum = %d,way = %s" % ((a+b+c),way))
```

可以把 `wrapper()` 函数指定关键字函数：

```python
def log(func):
    def wrapper(*args, **kwargs):
        print("call test()" )
        return func(*args, **kwargs)
    return wrapper
```

总的：

```python
def log(func):
    def wrapper(*args, **kwargs):
        print("call test()" )
        return func(*args, **kwargs)
    return wrapper

@log
def test(a,b,c,way=None):
    print("sum = %d,way = %s" % ((a+b+c),way))

test(2,4,5,"add")
```

![输出](http://upload-images.jianshu.io/upload_images/11861611-119ed74d4b096b7a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 三. 带参数的装饰器

装饰器还有更大的灵活性，例如带参数的装饰器，在上面的装饰器调用中，该装饰器接收唯一的参数就是业务函数 `test()`
装饰器的语法允许我们在调用时，提供其它参数，比如`@decorator(a)`。这样，就为装饰器的编写和使用提供了更大的**灵活性**。比如，我们可以在装饰器中指定日志的等级，因为不同业务函数可能需要的日志级别是不一样的。

```python
def log(level):
    def decorator(func):
        def wrapper(*args, **kwargs):
            if level == "warn":
                print("%s waring" % func.__name__)
            elif level == "info":
                print("%s infomation get" % func.__name__)
            return func(*args, **kwargs)
        return wrapper
    return decorator


@log(level="warn")
def test():
    print("this is what I want")

@log(level="info")
def test2():
    print("this is also what I want")

test()
test2()
```

上面的 `log()` 是允许带参数的装饰器。它实际上是对原有装饰器的一个函数封装，并返回一个装饰器。我们可以将它理解为一个含有参数的闭包。当我们用`@log(level="warn")`调用的时候，Python 能够发现这一层的封装，并把参数传递到装饰器的环境中。

### 四. 类装饰器

#### 定义
- 装饰器**不仅可以是函数，还可以是类**
- 相比函数装饰器，类装饰器具有灵活度大、高内聚、封装性等优点
- 像`__call__`这样前后都带下划线的方法在Python中被称为内置(魔法)方法。重载这些魔法方法一般会改变对象的内部行为

#### 用法
1. 让类的构造函数`__init__()`接受一个函数
2. **重载`__call__()`并 返回一个函数**
3. 使用` @类 `形式将装饰器附加到业务函数上

```python
class DecorateDemo(object):
    def  __init__(self, func):
        self.__func = func
    def  __call__(self):
        print("before class decorator")
        self.__func()
        print("after class decorator")
        return self.__func

@DecorateDemo
def test():
    print("this is what I want")

test()
```

###  五. functools模块

functools模块提供了两个装饰器，主要用 `functools.wraps`

使用装饰器极大地复用了代码，但是他有一个缺点就是原函数的元信息不见了：

```python
def log(func):
    def wrapper():
        print("call test()")
        return func()
    return wrapper

@log
def test():
    print("this is what I want")

test()
print test.__name__
```

wrapper

=-==-=-

```python
import functools

def log(func):
    @functools.wraps(func)
    def wrapper():
        print("call test()")
        return func()
    return wrapper

@log
def test():
    print("this is what I want")

test()
print test.__name__
```

test
=-=-=-

### 六. 多个装饰器
装饰器顺序

一个函数还可以同时定义多个装饰器，比如：

```python
@a
@b
@c
def f ():
    pass
```

它的执行顺序是从里到外，最先调用最里层的装饰器，最后调用最外层的装饰器，它等效于

```python
f = a(b(c(f)))
```

例子：

```python
def log(func):
    def wrapper():
        print("call test()")
        result = func()
        print("end test()")
        return result 
    return wrapper

def hello(func):
    def wrapper():
        print("hello test()")
        result = func()
        print("goodbye test()")
        return result 
    return wrapper

@log
@hello
def test():
    print("this is what I want")

test()
```



参考文章：
[Python装饰器由浅入深](https://blog.csdn.net/qq_37267015/article/details/71190979)
[廖雪峰python装饰器](https://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/001386819879946007bbf6ad052463ab18034f0254bf355000)
[理解 Python 装饰器看这一篇就够了](https://foofish.net/python-decorator.html)

