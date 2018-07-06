---
title: 类Instagram项目第一节
date: 2018-06-22 00:24:38
tags:
- python
- 项目
- Flask
categories: 项目
---
用python语言开发类似instagram网站的项目，写博客以整理思路形成系统的认知，加深印象和方便日后复习。
如发现问题，欢迎随时留言讨论。
本文主要介绍项目需要用到的python基础入门例子和一个小爬虫，python的系统理论知识不是本文重点。

<!-- more -->


# 基础知识

## 代码托管平台

*   GitHub
*   GitLab
*   BitBucket

## python手册
- 由于类库的关系，选择python2.7
- 在安装python的目录中可以找到。（...\Python27\Doc\python2713.chm）

## pycharm中的一些快捷键
1. Ctrl + /               行注释/取消行注释
2. Ctrl + Alt + L     代码格式化
3. Tab + Tab         缩进当前行    /  Shift +  Tab 不缩进当前行

## 内置函数

|方法 |作用|
|---|---|
|max() |最大值
|min()| 最小值
|len() |序列长度
|abs() |绝对值
|range() |整数列表
|dir() |属性、方法列表
|eval() |**执行**一个字符串表达式，并返回表达式的值
|chr() |ASCII -> 字符
|ord()| 字符 -> ASCII
|divmod() |包含、**商和余数**的元组

```python
x = 2  
y = 3.3  
print 1,x, y, type(x), type(y)       # 内置函数type()查看变量类型  
print 2, max(2, 1), min(5, 3)        # 最值  
print 3, len('xxx'), len([1, 2, 3])  # 字符串，数组长度  
print 4, abs(-2)                     # （c）fabs,（java）Math.fabs  # 绝对值  
print 5, range(1, 10, 3)             # 1到10， 步长3  输出[1, 4, 7]  
print 6, dir(list)                   # 把list类型下的函数名打印出来  
print 7, chr(97), ord('a')           # ASCII码97对应字符，字符a对应ASCII码，输出a 97  
print 8, divmod(11, 3)               # 求余，得3余2，输出(3, 2)  
```

```python
x = 2  
print eval('x + 3')                     # 字符串"x+3",输出5  
print repr(x == 1), type(repr(x == 1))  # 输出字符串"False"  
print repr(x + 1), type(repr(x + 1))    # 输出字符串3  
  
str_a = "[1,2,3,4]"  
print type(str_a)                       # 字符串str_a  
print eval(str_a), type(eval(str_a))    # 转化成list，输出[1, 2, 3, 4] <type 'list'>  
```

## 控制流
- if
- while
- for
- continue
- break
- pass 空语句，不做任何事情

例子：
0，1，2，3，4，6，7，8，9（跳过5）:

```python
for i in range(0, 10):  
    if i == 5:  
        pass  # 先不做任何处理，直接跳过，但是如果不写pass，就会语法错误  
    else:  
        print i  
```

## 字符串

|方法 |作用|
|---|---|
|capitalize()| 将字符串第一个字母变成大写，其它字母变成小写
|replace() |把字符串中的旧字符串替换为新字符串
|lstrip() |截掉字符串左边的空格或指定字符
|startswith() |字符串是否以指定子字符串开头
|endswith()| 字符串是否以指定字符串结尾
| len()|字符串长度
| join()|以指定字符连接序列元素以形成新的字符串（list→string）
| split()| 以指定字符分割字符串（string→list）
| find() |字符串是否包含子字符串

```python
stra = 'aaa'    
strb = 'bbbb'    
strc = 'hello world'    
print 3, strc.startswith('hel')     # 判断字符串是不是以hel开头，输出True    
print 4, strc.endswith('x')   # 判断字符串是不是以x结尾，输出False    
print 5, stra + strb + strc   # 字符串拼接，输出aaabbbbhello world    
print 6, len(strc)            # 字符串长度，输出11    
print 7, '-'.join(['a', 'b', 'c'])  # 把字符串之间以-连接，输出a-b-c    
print 8, strc.split(' ')      # 把字符串通过某字符分开为list，输出['hello', 'world']    
print 9, strc.find('hello')   # 查找指定字符串位置，输出0（字符串‘hello’从位置0开始）    
print 10, strc.find('ello')    # 输出1（字符串‘ello’从位置1开始）   
```

## 数据结构

### 列表--list[ ]

|方法 |作用|
|---|---|
|extend()| 连接两个列表
|in |某个元素是否在列表中
| +, * |操作符重载
| insert() |在指定位置插入元素
|pop() |弹出指定位置元素，默认弹出最后元素
|reverse() |反转列表
|sort() |排序

代码演示：

```python
lista = [1, 2, 3]  
print lista  # 输出：[1, 2, 3]

listb = ['a', 1, 'c', 1.1]   # 列表中的元素可以是【不同类型】的  
print listb   # 输出：['a', 1, 'c', 1.1]

# 列表b【连接】到列表a 后面   
lista.extend(listb)
print lista   #输出：[1, 2, 3, 'a', 1, 'c', 1.1]  

# （操作符重载）【添加】列表b的内容到列表a的后面  
lista = lista + listb             
print lista # 输出： [1, 2, 3, 'a', 1, 'c', 1.1, 'a', 1, 'c', 1.1]  
  
 # 列表【长度】（元素个数）
print  len(lista) #输出：11  

 # 元素‘a’【是否在】listb中
print  'a' in listb     #输出：True  
  
# 在listb的位置0之前【添加】一个元素‘www’
listb.insert(0, 'www')       
print listb   # 输出：['www', 'a', 1, 'c', 1.1]
  
# 【弹出】listb位置1的元素（"a"）
listb.pop(1)    
print listb    # 输出：['www', 1, 'c', 1.1]

 # 【逆序】
listb.reverse()  
print listb   # 输出：[1.1, 'c', 1, 'www']  

# 【排序】  
listb.sort()         
print listb  # 输出： [1, 1.1, 'c', 'www']  
 
 # 数组【索引访问】元素
print listb[0], listb[1] 
 # 输出：1.1 c 
   
print listb * 2   # 输出： ['www', 'c', 1.1, 1, 'www', 'c', 1.1, 1]  

print [0] * 5  # 输出：[0, 0, 0, 0, 0]
```

### 元组--tuple( )
元组和列表最大的区别在于：元组不能修改。
#### 创建元组

创建元组很简单，只需要将元素用括号(可选)括起来，并使用逗号分隔符(即使只有一个元素)来分隔各个元素即可：


```python
# -- coding: utf-8 --
fruits = ('apple','orange','banana')
none_tuple = ()
fruits_1 =('apple',)

# 输出：<type 'tuple'>
print type(fruits)
print type(none_tuple)
print type(fruits_1)
```

#### 使用tuple函数

通过tuple函数可以将一个序列转换为元组。如下：

```python
# -- coding: utf-8 --
temp_tuple = tuple([1,2,3])

# 输出：(1, 2, 3)
print temp_tuple
# 输出：('a', 'b', 'c')
print tuple('abc')
```

#### 哪些地方需要使用元组
- 元组可以在映射(和集合的成员)中作为键(key)使用，而列表不行
- 元组作为很多内建函数和方法的返回值存在

### 字典--dict {k1:v1, k2:v2}
字典中的值没有特定顺序，每个值都对应于一个唯一的键。键可以是数字、字符串甚至是元组。即哈希结构(key: value)。

|方法 |作用|
|---|---|
|get()| 返回指定键的值，如果键不存在则返回默认值None
|keys() |以列表返回一个字典所有的键
|values() |以列表返回一个字典所有的值
|has_key()| 字典是否包含某个键
|items()| 遍历一个字典
|pop() |删除给定键所对应的值，返回值为被删除的值
|del()| 删除元素


```python
dicta = {4: 16, 1: 1, 3: 9, 2: 4} 
print dicta   # 输出：{1: 1, 2: 4, 3: 9, 4: 16}

 # dicta[2]表示key为2对应的value
print dicta[2]   # 输出：4  

# 通过get()【访问】value
print dicta.get(2)   # 输出：4
  
# 【增加】新的键值对  
dicta['a'] = 'x'      
print dicta  # 输出：{'a': 'x', 1: 1, 2: 4, 3: 9, 4: 16}

# 以列表形式【输出】字典的所有
print dicta.keys(), dicta.values() # 输出：['a', 1, 2, 3, 4] ['x', 1, 4, 9, 16]

# 判断【查找】是否存在指定key（没有has_value）
print 6, dicta.has_key(1), dicta.has_key('3')    # 输出：True False
  
# 【遍历】key value（自动按key的排序输出）  
for k, v in dicta.items():  
    print 'key-value:', k, v  
# 输出：     key-value: a x
#           key-value: 1 1
#           key-value: 2 4
#           key-value: 3 9
#           key-value: 4 16

# 【删除】key为4的键值对    
dicta.pop(4)             
print dicta  # 输出： {'a': 'x', 1: 1, 2: 4, 3: 9}

# 【删除】key为3的键值对  
del dicta[3]         
print dicta # 输出： {'a': 'x', 1: 1, 2: 4}
```

### 集合--set([ ])
- set是一个无序不重复元素集
- 作为一个无序的集合，sets不记录元素位置或者插入点。因此，sets不支持 indexing, slicing, 或其它类序列（sequence-like）的操作

|符号 |作用|
|---|---|
|& |交集
| intersection |交集|
| \| | 并集
| union |并集|
| - |差集|

```python
lista = [1, 2, 3]  
seta = set(lista)  
setb = set((2, 3, 4))  
setc = {3, 4, 5}  
  
print seta,type(seta)  # 输出：set([1, 2, 3]) <type 'set'>
print setb,type(setb)  # 输出：set([2, 3, 4]) <type 'set'>
print setc,type(setc)  # 输出：set([3, 4, 5]) <type 'set'>
  
# 交集 
print seta & setb,seta.intersection(setb) # 输出：set([2, 3]) set([2, 3])

# 并集
print seta | setb, seta.union(setb)  # 输出：set([1, 2, 3, 4]) set([1, 2, 3, 4])

# 差
print seta - setb    # 输出：set([1])

# 差
print setb - seta    # 输出：set([4])
  
# 增加元素 
seta.add('x')          
print seta  # 输出： set([1, 2, 3, 'x'])
  
print len(seta)  # 输出：4
```

## 异常
Python用异常对象(exception object)表示异常情况，遇到错误后，会引发异常。如果异常对象并未被处理或捕捉，程序就会用所谓的回溯(Traceback,一种错误信息)终止执行。

- try
- except
- finally

### 捕捉异常
触发异常后，后面的代码就不会再执行

```python
try:
    print 1
    print 2 / 0          # 触发异常后，后面的代码就不会再执行
    print 3              
except Exception as e:   # 捕获异常，把这些异常信息print出来
    print 'error:', e
finally:                 # 不论是否发生异常，都会执行Finally语句后的代码
    print 'clean up'
```

- 输出

1
error: integer division or modulo by zero
clean up 

### raise 
Python中的raise 关键字用于引发一个异常，基本上和Java中的throw关键字相同

```python
try:  
    size = 'L'  
    if size == 'L':  
        raise Exception('Raise Error', 'NowCoder')  # 可以使用raise语句自己触发异常  
except Exception as e:  
    print 'error:', e  
finally:    
    print 'clean up'  
```

- 输出

error: ('Raise Error', 'NowCoder')
clean up

### 捕捉多个异常

```python
try:  
    print 2/0  
except ZeroDivisionError:  
    print '除数不能为0'  
except Exception:  
    print '其他类型异常'
```

- 输出

除数不能为0


## 随机数
- 计算机的随机数都是伪随机
- 所谓“随机码”，就是无论这个码有多长都不会出现循环的现象，而“伪随机码”在码长达到一定程度时会从其第一位开始循环

|方法 |作用|
|---|---|
|seed()    |   改变随机数生成器的种子，可以在调用其他随机模块函数之前调用此函数
|random()   |返回[0,1)之间的浮点数。不能直接访问的，需要通过 random 静态对象调用该方法
|randint(M,N) | 返回[M,N]的整数
|choice()   |   返回一个列表，元组或字符串的随机项   
|range()    |    创建一个整数列表。range(10)不包含10。
|shuffle()  |   将序列的所有元素随机排序


```python
import random  
  
random.seed(2)      # 指定seed之后，每次随机出来的值被固定  
  
 # 返回[0,1)之间的浮点数  
print random.random()       # 输出：0.956034271889

# [0,100)的浮点数  
print random.random() * 100         # 输出： 94.7827487059

# [0,100)的整数
print  int(random.random() * 100)      # 输出：  5

# [0,100]的整数,包含100
print random.randint(0, 100)          # 输出： 8
  
# [0,100),步长为10，随机抽取1个数 
print random.choice(range(0, 100, 10))    # 输出： 80
  
# 抽样[0,100)，随机抽取4个数   
print random.sample(range(0, 100), 4)   # 输出： [73, 66, 30, 60]
  
# 随机打乱列表  
a = [1, 2, 3, 4, 5]  
random.shuffle(a)            
print a    # 输出：  [2, 5, 1, 3, 4]
```

## 正则

|符号|匹配|
|---|---|
|\d|数字 
|\D|非数字 
|+ |至少匹配1次 
|*| 至少匹配0次

|方法|简介|作用|
|---|---|---|
|re.compile()   |创建 |根据包含正则表达式的字符串创建模式对象
|模式.findall()  | 匹配|以列表的形式返回能匹配的子串

```python
str = 'abc123def12gh15'  
# 创建，正则表达式的模式——pattern  
p1 = re.compile('[\d]+')   # 匹配1个或多个数字
p2 = re.compile('\d')      # 匹配1个数字  
# 匹配，以列表的形式返回匹配的子串  
print p1.findall(str)     # 输出：['123', '12', '15']
print p2.findall(str)     # 输出：['1', '2', '3', '1', '2', '1', '5']


# email的正则表达式    
str = 'a@163.com;b@gmail.com;c@qq.com;e0@163.com;z@qq.com'  
p3 = re.compile('[\w]+@[163|qq]+\.com')                     
print p3.findall(str)   
# 输出： ['[a@163.com]', '[c@qq.com], '[e0@163.com', '[z@qq.com]']


str = '<html><h>title</h><body>xxx</body></html>'  
p4 = re.compile('<h>[^<]+</h>')   
print p4.findall(str)      # 输出：['<h>title</h>']

p4 = re.compile('<h>([^<]+)</h>')        # 加()匹配输出括号内的字符串
print p4.findall(str)          # 输出：['title']

p4 = re.compile('<h>([^<]+)</h><body>([^<]+)</body>')  # 运用()提取多个  
print p4.findall(str)      # 输出：[('title', 'xxx')]
  

str = 'xx2016-06-11yy'  
p5 = re.compile('\d\d\d\d-\d\d-\d\d')  
print p5.findall(str)   # 输出：['2016-06-11']

p5 = re.compile('\d{4}-\d{2}-\d{2}')           # 用{}表示次数  
print p5.findall(str)  # 输出：['2016-06-11']
```

# 实战

## 搭环境
使用python版本是2.7，IDE是PyCharm，版本管理工具是git

PyCharm需要进行简单的配置
    在File>Settings>Version Control>GitHub里面进行设置项目地址，github账号
    在File>Settings>Version Control>Git里面设置git.exe所在地址，一般在安装git路径的bin文件夹里面

pip安装包的命令`pip install XXXX`



## 简单抓取网页内容 c1_01.py

### 第一步
这里用到python的两个包，需要使用pip安装一下
    `pip install requests`
    `pip install bs4`
    `pip install BeautifulSoup`

也可以使用PyCharm安装，在File>Settings>Project:XXX>Project Interpreter里面双击pip

### 第二步

然后直接在搜索框中输入包的名字

```
#-*- encoding=UTF-8 -*-
import requests
from bs4 import BeautifulSoup

def qiushibaike():
    #得到网页的内容
    content = requests.get('http://www.qiushibaike.com').content
    #解析
    soup = BeautifulSoup(content,'html.parser')

    #查找在div里面class是content里面的内容
    for div in soup.find_all('div', {'class':'content'}):
        print div.text.strip()

if __name__ == '__main__':
    def qiushibaike()
```

![注意正文在content里](http://upload-images.jianshu.io/upload_images/11861611-74444fa02417ebb6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


参考文章
[总结](https://blog.csdn.net/mnlmj/article/details/75156639)