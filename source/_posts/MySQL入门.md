---
title: MySQL入门
date: 2018-05-23 19:02:49
tags: MySQL
categories: 数据库 
---
- 本文主要内容来自[慕课网](http://www.imooc.com/)。配合[视频](https://www.imooc.com/learn/122)食用口味更佳
- 主要是顺着已经学习的视频顺序总结一遍，以深化理解和方便日后复习
- 添加了自己的实践和理解
<!-- more -->
#  第一章：初涉MySQL 
##  MySQL概述
MySQL是一个开源的关系型数据库管理系统，由瑞典MySQL AB公司开发，后被Oracle收购，主要分为社区版和企业版。
MySQL由于性能高、成本低、可靠性好，已经成为最流行的开源数据库，因此被广泛地应用在Internet上的中小型网站中。随着MySQL的不断成熟，它也逐渐用于更多大规模网站和应用，比如维基百科、Google和Facebook等网站。非常流行的开源软件组合LAMP中的“M”指的就是MySQL。
##  MySQL目录结构
-  bin：存储可执行文件
-  data：存储数据文件（以后创建的数据库和索引文件都可以放在这里）
-   docs：文档
-   include：存储包含的头文件
-   lib：存储库文件
-  share：错误消息和字符集文集
##  启动与停止MySQL服务
利用cmd开启和关闭MySQL服务
1. 用管理员的身份打开cmd（必须是管理员是身份打开，否则会报错）
2.  启动Mysql服务

```
net start mysql
```

3.  停止Mysql服务

```
net stop mysql
```

##  Mysql的常用参数


|语法| 含义|
|---|---|
|-p, --password[=name]       |   密码
|-P, --port=#                 |          端口号
|-h, --host=name           |         服务器名称
|-D, --database=name     |       打开指定数据库
|--delimiter = name         |       指定分隔符
|--prompt=name              |      设置提示符
|-V,--version                   |         输出版本信息并退出（必须是大写V）
|-u, --user=name           |         用户名

## 登录与退出
### 登陆

```
mysql -uroot -pmima -P3306 -h127.0.0.1
```

-u后面是用户名
-p后面是密码
-P后面是端口号
-h后面是服务器名称，127.0.0.1是本地服务器
如果没有修改端口号，而且是本地服务器，则可以省略-P3306 -h127.0.0.1这两个参数
在-p后面直接输入密码会显示在当前窗口的标题栏中，这样是不安全的，我们可以直接输入mysql -uroot -p
后回车，就可以在密码框中输入密码了。
### 退出
主要有三种退出方式：

1. 

```mysql  
mysql  >  exit;
```

2. 

```mysql  
mysql  >  quit;
```

3. 
```mysql  
mysql > \q;
```


##  MySQL常用命令--SELECT
显示当前服务器版本

```mysql
SELECT VERSION();
```

显示当前日期时间


```mysql
SELECT NOW();
```

显示当前用户

```mysql
SELECT USER();
```

![SELECT USER();](http://p7vxw6hv7.bkt.clouddn.com/18-5-23/70603506.jpg)

##  MySQL语句规范
- 关键字与函数名称全部大写
- 数据库名称、表名称、字段名称全部小写
- SQL语句必须以分号结尾
##  操作数据库
### 创建数据库--CREATE

创建数据库的语法结构：

```mysql
CREATE {DATABASE | SCHEMA} [IF NOT EXISTS] db_name 
[DEFAULT] CHARACTER SET [=] charset_name
```

{}是必选项，[]是可选项，|是做选择项
比如创建一个名为test1的数据库：

```mysql
CREATE DATABASE test1;
```

### 查看数据库列表--SHOW

查看当前服务器下的数据库列表语法结构：

```mysql
SHOW {DATABASES | SCHEMAS}    
[LIKE 'pattern' | WHERE expr]
```

比如，查看上面创建的test1数据库是否成功

```mysql
SHOW DATABASES;
```

![SHOW DATABASES](http://p7vxw6hv7.bkt.clouddn.com/18-5-23/45561703.jpg)

### 修改数据库--ALTER

修改数据库的语法结构：

```mysql
ALTER {DATABASE | SCHEMA}  [db_name]        
[DEFAULT]  CHARACTER SET [=] charset_name
```

比如将刚刚创建的test2的编码方式修改为utf8

```mysql
ALTER DATABASE test2 CHARACTER SET utf8;
```

### 删除数据库--DROP

```mysql
DROP {DATABASE | SCHEMA} [IF EXISTS] db_name
```

比如删除我们上面创建的test2数据库

```mysql
 DROP  DATABASE test2;
```

### 忽略错误产生
- 有ERROR
我们创建了一个名为test1数据库后，如果再次创建一个同名的数据库：

```mysql
CREATE DATABASE test1;
```

会提示ERROR 1007 (HY000): Can't create database 'test1'; database exists错误
- ERROR转换成WARNINGS
但是如果加上IF NOT EXISTS，将忽略错误的产生，并产生一个警告。

```mysql
CREATE DATABASE IF NOT EXISTS test1;
```

- 查看WARNINGS

```mysql
SHOW WARNINGS;
```

### 编码信息--SHOW CREATE DATABASE
- 查看

```mysql
SHOW CREATE DATABASE 数据库名字;
```

![SHOW CREATE DATABASE](http://p7vxw6hv7.bkt.clouddn.com/18-5-23/8239695.jpg)

- 也可以在创建数据的时候，指定相应的编码方式

```mysql
CREATE DATABASE test2 CHARACTER SET 编码方式;
```

# 第二章：数据类型与操作数据表 
## 数据类型
- 整型
![整型](http://p7vxw6hv7.bkt.clouddn.com/18-5-22/59717294.jpg)
- 浮点型
![浮点型](http://p7vxw6hv7.bkt.clouddn.com/18-5-22/63434388.jpg)
- 日期时间型
![日期时间型](http://p7vxw6hv7.bkt.clouddn.com/18-5-22/48811540.jpg)
- 字符型
![字符型](http://p7vxw6hv7.bkt.clouddn.com/18-5-22/68047806.jpg)
## 创建数据表---CREATE 
### 数据表TABLE
数据表又称表，是数据库最重要的组成部分之一，是其他对象的基础
行称之为记录，列称之为字段
### 创建数据表
创建数据表

1.  打开数据库--USE

```mysql
USE 数据库名;
```

2.  检查打开的数据库是否正确--SELECT

```mysql
SELECT DATABASE();
```

3.  创建数据表的语法结构--CREATE 

```mysql
CREATE TABLE [IF NOT EXISTS] table_name (
column_name  data_type,   ……）
```

column_name是列名称，数据表有多少列需要根据项目分析事先规划好
data_types是数据类型, 逗号是两个字段之间的分隔符，最后一个字段不用加逗号
### 举例
我们创建一个简单是数据表，包含以下几项信息：

用户名：username ，用户名一般是字符型

年龄：age ，年龄一般是整型，另外年龄不可能是负值，所以常设置无符号位

工资：salary，工资一般设置为浮点型

```mysql
mysql> CREATE TABLE tb1(
    -> username VARCHAR(20),
    -> age TINYINT UNSIGNED,
    -> salary FLOAT(8,2) UNSIGNED
    -> );
```

## 查看数据表
### 查看数据表列表--SHOW TABLES
- 看的是**名字**
查看数据表列表语法结构

```mysql
SHOW TABLES [FROM db_name]
[LIKE 'pattern' | WHERE expr]
```

![SHOW TABLES](http://p7vxw6hv7.bkt.clouddn.com/18-5-23/66318358.jpg)

- 查看当前数据库的数据表列表：

```mysql
SHOW TABLES;
```

- 不仅可以查看当前数据库下的数据表列表，还可以查看其它数据库的数据表列表，比如查看MySQL数据库中所有的数据表列表

```mysql
SHOW TABLES FROM mysql;
```

### 查看数据表结构--SHOW COLUMNS...FROM 
- 看的是**具体信息**
查看数据表结构

```mysql
SHOW COLUMNS FROM tbl_name
```

比如查看名为tb1这个列的数据表结构

```
SHOW COLUMNS FROM tb1;
```

![SHOW COLUMNS ](http://p7vxw6hv7.bkt.clouddn.com/18-5-23/37637774.jpg)

## 记录的插入与查找
### 插入记录--INSERT...VALUES
- 插入记录语法结构

```mysql
INSERT [INTO] tbl_name[(col_name,...)] VALUES(val,...)
```

tbl_name：表名字。  col_name：列名字。  VALUES：值

- 如果省略列名，需要对所有的字段都赋值

```mysql
INSERT tb1 VALUES('datiangou',24,4000);
```

- 也可以只给部分字段赋值，比如

```mysql
INSERT tb1(username,salary) VALUES('cimutongzi',14000);
```

### 记录查找---SELECT ...FROM 

记录查找语句

```mysql
SELECT expr,... FROM tbl_name
```

比如：（这里的*是字段的过滤）

```mysql
SELECT * FROM tb1;
```

显示如下：
![SELECT * FROM](http://p7vxw6hv7.bkt.clouddn.com/18-5-23/72281528.jpg)

## 空值与非空
- NULL，字段值可以为空
- NOT NULL，字段值禁止为空

1. 我们首先创建一个字段，**设置username 为NOT NULL**

```mysql
mysql> CREATE TABLE tb2(
    -> username VARCHAR(20) NOT NULL,
    -> age TINYINT UNSIGNED NULL
    -> );
```

2. 然后查看这个字段的结构，看到**username 为NOT NULL**
3. 我们插入一条数据，尝试使得该数据username为NULL：

```mysql
mysql> INSERT tb2 VALUES(NULL,26);
```

4. ERROR 1048 (23000): Column 'username' cannot be null。可以看到报错提示不允许username为NULL

![不允许插入NULL到NULL值处](http://p7vxw6hv7.bkt.clouddn.com/18-5-23/98589114.jpg)

## 自动编号 AUTO_INCREMENT


- 自动编号，且必须与主键组合使用

- 默认情况下，起始值为1，每次的增量为1，依次递增。

所以该字段数据类型一定是数值型：整数，浮点数都可以，浮点数小数位数必须为0

举例：

```mysql
mysql> CREATE TABLE tb3(
    -> id SMALLINT UNSIGNED AUTO_INCREMENT,
    -> username VARCHAR(30) NOT NULL
    -> );
```

ERROR 1075 (42000): Incorrect table definition; there can be only one auto column and it must be defined as a key
- 可以看到报错了，这是因为这里的id，作为自动编号字段**必须**设置成主键才可以
## 初涉约束
### 主键约束
本节参考[MySQL 为什么需要一个主键](https://ruby-china.org/topics/26352) 
####  主键原则
- 主键（primary key） ：**唯一**标识表中每行的这个列（或这组列）称为主键。其值能够唯一区分表中的每个行。 
- 虽然并不总是都需要主键，但大多数数据库设计人员都应保证他们创建的每个表有一个主键，以便于以后数据操纵和管理。没有主键，更新或删除表中特定行很困难，因为没有安全的方法保证只设计相关的行。
- 表中的任何列都可以作为主键，只要它满足一下条件：
     - 唯一
任何两行都不具有相同的主键值 
     - 非NULL
每个行都必须具有一个主键值（主键列不允许NULL值）
- AUTO_INCREMENT必须和主键一起使用，但是主键不一定必须和AUTO_INCREMENT使用
#### 几个好习惯
- **不更新**主键列的值
- **不重用**主键列的值
-  在主键列中**不使用**可能会更改的值（例如，如果使用一个名字作为主键以标识某个供应商，应该供应商合并和更改其名字时，必须更改这个主键）
- 总之：不应该使用一个具有意义的column（id 本身并不保存表 有意义信息） 作为主键，并且一个表必须要有一个主键，为方便扩展、松耦合，高可用的系统做铺垫。
#### 主键其他作用
- 磁盘存储
- InnoDB 存储引擎采用了聚集（clustered）的方式，因此每张表的存储都是按主键的顺序进行存放。如果没有显式地在表定义时指定主键，InnoDB存储引擎会为每一行生成一个6字节的ROWID，并一次作为主键。
无特殊需求下Innodb建议使用**与业务无关的自增ID**作为主键
#### 设置主键--PRIMARY KEY
我们将上节中的id定义成主键

```mysql
mysql> CREATE TABLE tb3(
    -> id SMALLINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    -> username VARCHAR(30) NOT NULL
    -> );
```

Query OK, 0 rows affected (0.01 sec)
可以看到，创建成功了，主键**可以写PRIMARY KEY,也可以直接写KEY**

现在再来看一下数据表结构：

![设置主键](http://p7vxw6hv7.bkt.clouddn.com/18-5-24/94624722.jpg)

可以看到，id不能为空，因为**主键自动为NOT NULL**，Key被定义成了PRI，Extra被定义成了自动编号，**则写入记录的时候id不需要专门赋值了**，我们插入三条记录：

![测试主键](http://p7vxw6hv7.bkt.clouddn.com/18-5-24/18737645.jpg)

然后查看一下记录：

![主键自增](http://p7vxw6hv7.bkt.clouddn.com/18-5-24/19797907.jpg)

可以看到id被设计AUTO_INCREMENT 的主键，被自动编号成了123
### 唯一约束  
#### 唯一约束原则
- 因为主键一张表只有一个，如果想保证**值的（记录）**唯一性，可以使用UNIQUE KEY（唯一约束）
- 唯一约束的字段可以为空值(NULL)（存储的时候，多个空值只会保存一个空值，所以并不违背唯一性）
- 每张数据表可以存在**多个**唯一约束
#### 创建唯一约束-- UNIQUE KEY

我们创建一个既有主键约束，又有唯一约束的数据表：

```mysql
mysql> CREATE TABLE tb5(
    -> id SMALLINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    -> username VARCHAR(30) NOT NULL UNIQUE KEY,
    -> age TINYINT UNSIGNED
    -> );
```

查看一下数据表的结构：

![加入唯一约束](http://p7vxw6hv7.bkt.clouddn.com/18-5-24/22854663.jpg)

可以看到，id为主键约束，username为唯一约束，我们写入一些记录：

![测试唯一约束](http://p7vxw6hv7.bkt.clouddn.com/18-5-24/99929570.jpg)

可以发现username不能重复
### 默认约束--DEFAULT 
当插入记录时，**如果没有明确为字段赋值，则自动赋予默认值**

我们创建一个数据表：

```mysql
mysql> CREATE TABLE tb61(
    -> id SMALLINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    -> username VARCHAR(20) NOT NULL UNIQUE KEY,
    -> sex ENUM('1','2','3') DEFAULT '3'
    -> );
```

查看一下数据表结构：

![加入默认值](http://p7vxw6hv7.bkt.clouddn.com/18-5-24/12573762.jpg)

可以看到sex字段的默认值为3

我们插入一条记录，仅设置username，不设置sex

```mysql
mysql> INSERT tb6(username) VALUES('jiutuntongzi');
```

然后查看一下记录：

![测试默认约束](http://p7vxw6hv7.bkt.clouddn.com/18-5-24/70353474.jpg)

发现即使我们没有设置 jiutuntongzi 的性别，但是其sex**默认被赋值成了**3(DEFAULT 值)

# 第三章：约束以及修改数据表 
## 约束意义和分类
- 约束保证数据的完整性和一致性。
- 约束分为表级约束和列级约束。
- 分类
NOT NULL(非空约束)
PRIMARY KEY(主键约束)
UNIQUE KEY(唯一约束)
DEFAULT(默认约束)
FOREIGN KEY(外键约束)
## 外键约束-- FOREIGN KEY...REFERENCES
外键是表中的一列，其值必须在另一表的主键中。即一个表中的 FOREIGN KEY 指向另一个表中的 PRIMARY KEY。
### 目的
- 外键约束是为了保持数据一致性和完整性。外键是保证引用完整性的极其重要的部分。
- 防止意外删除
### 要求
- 父表和子表必须使用相同的存储**引擎**，而且禁止使用临时表。
- 数据表的存储引擎只能为InnoDB
- 外键列和参照列必须具有相似的**数据类型，其中数字的长度或是否有符号位必须相同**，而字符的长度则可以不同。
- 外键列和参照列必须创建索引。如果外键列不存在索引的话，MySQL将自动创建索引( 如果参照列不存在索引的话，mysql不会自动创建索引。但如果参照列为主键的话，则会自动创建索引。主键在创建的同时会自动创建索引，所以参照列其实已经有了索引。)
## 表级约束与列级约束
### 定义
- 表级约束
对多个数据列建立的约束，称为表级约束表级约束只能在列定义后声明
- 列级约束
对一个数据列建立的约束，称为列级约束列级约束既可以在列定义时声明，也可以在列定义后声明
### 使用情况
- 我们在平常的开发中，多用到列级约束，表级约束很少用到在所有的约束中
- 并不是每一种约束，都存在列级约束和表级约束
比如 NOT NULL ，DEFAULT 就没有表级约束，只有列级约束。而PRIMARY KEY， UNIQUE KEY，FOREIGN KEY都可以存在表级约束和列级约束。



## 修改数据表
### 添加/删除列
#### 添加列--ALTER TABLE...ADD
##### 语法
- 添加**单列**的语法结构：

```mysql
ALTER TABLE tbl_name ADD [COLUMN]   col_name column_definition [FIRST | AFTER col_name ]
```

- 其中， FRIST指的是添加的列置于最前面，AFTER指的是添加列置于**指定列的后面**
- 如果不写FRIST或者AFTER，则默认添加到所有列的最下面
- **多列**的添加不能指定位置关系，只能默认添加到最下面。
```mysql
ALTER TABLE tbl_name ADD [COLUMN]   (col_name column_definition,...) 
```

##### 例子
用之前的tb6做例子：

![插入列之前的tb6](http://p7vxw6hv7.bkt.clouddn.com/18-5-24/12573762.jpg)

- 不写FRIST或者AFTER，则默认添加到所有列的最下面

```mysql
mysql> ALTER TABLE tb6 ADD age TINYINT UNSIGNED NOT NULL DEFAULT 18;
```

结果

![新列插在列尾](http://p7vxw6hv7.bkt.clouddn.com/18-5-24/10842982.jpg)

- 接来下我们再来加一个password字段，并置于username后面，则需要用到AFTER：

```mysql
mysql> ALTER TABLE users1 ADD password VARCHAR(32) NOT NULL AFTER username;
```

结果

![在指定列后添加新列](http://p7vxw6hv7.bkt.clouddn.com/18-5-24/86272896.jpg)

#### 删除列--ALTER TABLE...DROP
##### 语法
删除列的语法结构：

```mysql
ALTER TABLE tbl_name DROP [COLUMN] col_name
```

##### 例子
删除刚才给tb6添加的age字段

```mysql
mysql> ALTER TABLE tb6 DROP age;
```

结果

![删除列](http://p7vxw6hv7.bkt.clouddn.com/18-5-24/17558038.jpg)

### 添加/删除约束
#### 添加主键约束--ALTER TABLE...ADD PRIMARY KEY
##### 语法

```mysql
ALTER TABLE tbl_name ADD [CONSTRAINT [symbol]]
PRIMARY KEY [index_type](index_col_name,...)
```

##### 例子
创建一个测试用的数据表user2：

```mysql
mysql> CREATE TABLE user2(
    -> username VARCHAR(10) NOT NULL,
    -> pid SMALLINT UNSIGNED
    -> );
```

首先增加一列：

```mysql
mysql> AlTER TABLE user2 ADD id SMALLINT UNSIGNED;
```

此时表的结构：

![添加主键前](http://p7vxw6hv7.bkt.clouddn.com/18-5-25/64941405.jpg)

给id列添加主键约束：

```mysql
mysql> ALTER TABLE user2 ADD CONSTRAINT  PRIMARY KEY(id);
```

此时表的结构：

![添加id为主键](http://p7vxw6hv7.bkt.clouddn.com/18-5-25/23908217.jpg)

#### 添加唯一约束--ALTER TABLE...ADD UNIQUE
##### 语法

```mysql
ALTER TABLE tbl_name ADD [CONSTRAINT [symbol]]
UNIQUE [INDEX|KEY] [index_name] [index_type]
(index_col_nam,...)
```

##### 例子
唯一约束可以有多个，我们给usersname添加唯一约束：

```mysql
mysql> ALTER TABLE user2 ADD UNIQUE (username);
```

此时表的结构：

![插入唯一约束](http://p7vxw6hv7.bkt.clouddn.com/18-5-25/72009401.jpg)

#### 添加外键约束-ALTER TABLE..ADD FOREIGN KEY REFERENCES..
##### 语法

```mysql
ALTER TABLE tbl_name ADD [CONSTRAINT [symbol]]
FOREIGN KEY [index_name] (index_col_name,...)
reference_definition
```

##### 例子
将users2中的pid参照tb6中id，则需要给users2中的pid设置外键约束：

```mysql
mysql> ALTER TABLE user2 ADD FOREIGN KEY(pid) REFERENCES tb6(id);
```

此时表的结构：

![添加外键约束](http://p7vxw6hv7.bkt.clouddn.com/18-5-25/95500836.jpg)


#### 添加/删除默认约束-ALTER TABLE..ALTER..SET|DROP DEFAULT
##### 语法
添加/删除默认约束的语法结构：

```mysql
ALTER TABLE tbl_name ALTER [COLUMN] col_name
{SET DEFAULT literal| DROP DEFAULT}
```

##### 例子
在user2表中添加一个age字段：

```mysql
mysql> ALTER TABLE  user2 ADD age TINYINT UNSIGNED NOT NULL;
```

此时表的结构：

![添加默认约束前](http://p7vxw6hv7.bkt.clouddn.com/18-5-25/68226904.jpg)

可以看到age字段并没有默认值，接下来添加默认约束：

```mysql
mysql> ALTER TABLE user2 ALTER age SET DEFAULT 18;
```

此时表的结构：

![添加age默认约束值18](http://p7vxw6hv7.bkt.clouddn.com/18-5-25/57242040.jpg)

接下来删除默认约束

```mysql
mysql> ALTER TABLE user2 ALTER age DROP DEFAULT;
```

此时表的结构：

![删除age的默认约束](http://p7vxw6hv7.bkt.clouddn.com/18-5-25/89424665.jpg)


### 修改列定义,列名称，表名称
#### 修改列定义--ALTER TABLE...MODIFY...
- 数据类型
- 列的位置
##### 语法

```mysql
ALTER TABLE tbl_name MODIFY [COLUMN] col_name
column_definition  [FIRST| AFTER col_name]
```

##### 例子
比如users2表中的id并没有处于第一位置，虽然没有什么影响，但是不符合我们平常的习惯，可以将id字段修改为第一位置：

```mysql
mysql> ALTER TABLE user2 MODIFY id SMALLINT UNSIGNED NOT NULL FIRST;
```
 
可以看到id：
- 类型变成了MEDIUMINT UNSIGNED 
- 处在了第一列

![修改了id列的定义](http://p7vxw6hv7.bkt.clouddn.com/18-5-25/72970555.jpg)

修改数据类型时，**将大类型修改为小类型，有可能造成数据丢失。**

#### 修改列名称--ALTER TABLE...CHANGE...
##### 语法

```mysql
ALTER TABLE tbl_name CHANGE [COLUMN] old_col_name
new_col_name column_definition  [FIRST| AFTER col_name]
```

##### 例子
我们修改一下pid的数据类型和名字：

```mysql
mysql> ALTER TABLE user2 CHANGE pid p_id TINYINT UNSIGNED NOT NULL;
```

可以看到pid已经被修改为new_pid：

![修改列名字](http://p7vxw6hv7.bkt.clouddn.com/18-5-25/52347327.jpg)

#### 修改数据表名称--...RENAME TO...
尽量不要随意修改数据表名。
##### 语法
修改数据表名称方法一：

```mysql
ALTER TABLE tbl_name RENAME [TO|AS] new_tbl_name
```

修改数据表名称方法二（可以修改多个数据表）：

```mysl
RENAME TABLE tbl_name TO new_tbl_name
[, tbl_name2 TO new_tbl_name2]...
```

##### 例子
我们用方法一将user2名字修改为new_user2：

```mysql
mysql> ALTER TABLE users2 RENAME users3;
```

查看表名：

![修改表名](http://p7vxw6hv7.bkt.clouddn.com/18-5-25/74713696.jpg)

再用方法二将tb1,tb2分别改为new_tb1,new_tb2：

```mysql
mysql> RENAME TABLE users3 TO users2;
```

查看表名：

![修改表名](http://p7vxw6hv7.bkt.clouddn.com/18-5-25/79481377.jpg)

# 第四章：操作数据表中的记录 
本章内容在前几章有部分体现，这里做一个补充与总结。
##  插入记录--INSERT...VALUES...
### 语法

```mysql
INSERT [INTO] tbl_name [(col_name,...)] {VALUES| VALUE}
({expr | DEFAULT},...),(...),...
```

- col_name是列名称，**省略**不写则代表**所有**的字段需要依次赋值
- 对于自动编号的字段，赋值为NULL或者DEFAULT
### 例子
首先创建一个用于测试的数据表：

```mysql
mysql> CREATE TABLE users(
    -> id SMALLINT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
    -> username VARCHAR(20) NOT NULL,
    -> password VARCHAR(32) NOT NULL,
    -> age TINYINT UNSIGNED NOT NULL DEFAULT 10,
    -> sex BOOLEAN
    -> );
```

现在我们省略col_name，则插入记录的时候需要依次为所有字段赋值，自动编号的id字段可以NULL或者DEFAULT 

```mysql
mysql> INSERT users VALUES(NULL,'datiangou','123456',18,1);
mysql> INSERT users VALUES(DEFAULT,'cimutongzi','123456',24,1);
```

查看表中记录：

![插入记录](http://p7vxw6hv7.bkt.clouddn.com/18-5-25/79735183.jpg)

接下来一次性写入多条记录，只需要**用逗号分隔即可**，这里以两条记录为例：

```mysql
mysql> INSERT users VALUES(DEFAULT,'yuzaoqian','098765',25,1),
(NULL,'xuetongzi','098765',DEFAULT,0);
```

查看表中记录：

![](http://p7vxw6hv7.bkt.clouddn.com/18-5-28/11249367.jpg)

~~## 插入记录--INSERT SET-SELECT~~


## 单表更新记录--UPDATE...SET...
### 语法

```mysql
UPDATE [LOW_PRIORITY] [IGNORE] table_reference SET
col_name1={expr1 | DEFAULT} [,col_name2={expr2 | DEFAULT}]...
[WHERE where_condition]
```

expr1 | DEFAULT 是指表达式或者默认值
- WHERE where_condition如果省略，则会更新所有记录
### 例子

-  WHERE 语句省略，则更新所有记录
比如给所有用户年龄加5岁：

```mysql
mysql> UPDATE users set age=age+5;
```

查看表中记录：
可以看到，表中每一条记录的age值都被更新了。

![不限定范围更新数据](http://p7vxw6hv7.bkt.clouddn.com/18-5-28/35223447.jpg)

- 有WHERE语句，则特定条件的记录被更新
比如给所有偶数id用户年龄加10岁：

```mysql
mysql> UPDATE users SER age = age + 10 WHERE id % 2 == 0 ;
```

查看表中记录：
可以看到，表中id为偶数记录的age值都被更新了。

![特定范围更新数据](http://p7vxw6hv7.bkt.clouddn.com/18-5-28/63492586.jpg)


## 单表删除记录--DELETE FROM...
### 语法

```mysql
DELETE FROM tbl_name [WHERE where_condition]
```

### 例子
比如删除id为2的用户记录：

```mysql
mysql> DELETE FROM users WHERE id = 2;
```

查看表中记录：

![删除记录](http://p7vxw6hv7.bkt.clouddn.com/18-5-28/94997700.jpg)

可以看到id为2的用户已经被删除，如果此时再插入一条记录，id会为5，而不是2：

```mysql
mysql> INSERT users VALUES(NULL,'huiyeji','1234',12,NULL);
```
查看表中记录：

![删除之后，主键不会“自动填充”](http://p7vxw6hv7.bkt.clouddn.com/18-5-28/82824666.jpg)

## 查询表达式解析--SELECT...FROM...
### 语法

```mysql
SELECT select_expr [,select_expr...]
[
FROM table_references
[WHERE where_condition]
[GROUP BY {col_name | position} [ASC| DESC],...]
[HAVING where_condition]
[ORDER BY {col_name | expr | position} [ASC | DESC],...]
[LIMIT {[offset,] row_count | row_count OFFSET offset}]
]
```

每一个表达式表示想要的一列，**必须有至少一个**。多个列之间以英文逗号分隔。
### 例子
-  查询指定列
比如我想查找id和username这两列的记录：

```mysql
mysql> SELECT id, username FROM users;
```

查看表中记录：

![查询指定列](http://p7vxw6hv7.bkt.clouddn.com/18-5-28/73871525.jpg)

-  星号(*)表示所有列。

- “表名字.列名字” 级联结构，在使用多表链接时，该方法可以用于区别不同数据表的同名字段。

-   查询表达式可以使用[AS] alias_name为其赋予别名。**本名AS别名**
比如有时候一些字段的名字非常长，或者为了体现数据的意义，就可以赋予别名：

```mysql
mysql> SELECT id AS userID,username AS uname FROM users;
```

查看表中记录：

![别名](http://p7vxw6hv7.bkt.clouddn.com/18-5-28/20000588.jpg)

## 条件查询--WHERE
条件表达式用于对记录进行过滤，如果没有指定WHERE子句，则显示所有的记录，在WHERE表达式中，可以用MysSQL支持的**函数或运算符**
## 语句对查询结果分组--GRUOP BY
- GROUP BY关键字可以将查询结果按照某个字段或多个字段进行分组。字段值**相等的为一组。**
- 分组就是将一个数据集划分成若干个小区域，然后再针对若干个小区域进行数据处理。本文将介绍mysql使用group by分组时，实现组内排序的方法。
- 单独使用group by没有任何意义，它的真正作用在于与各种组合函数配合
### 语法

```mysql
[GROUP BY {col_name | position}] [ASC | DESC],...]
```

### 例子
比如对性别进行分组：

```mysql
mysql> SELECT sex FROM users GROUP BY sex;
```

查看表中记录：

![分组](http://p7vxw6hv7.bkt.clouddn.com/18-5-30/7956689.jpg)

## 语句设置分组条件--HAVING
WHERE过滤行， HAVING过滤分组
### 语法

```mysql
[HAVING where_condition]
```

- HAVING后面的条件只能为聚合函数（比如SUM，MIN，MAX等）
- 否则该条件必须出现在SELECT中
### 例子
比如对年龄大于35岁的用于进行性别分组：

```mysql
mysql> SELECT sex,age FROM users GROUP BY sex HAVING age > 35;
```

查看表中记录：

![](http://p7vxw6hv7.bkt.clouddn.com/18-5-30/49138731.jpg)

## 语句对查询结果排序--ORDER BY
### 语法

```mysql
[ORDER BY {col_name | expr | position}[ASC | DESC],...]
```

- ASC：升序（默认）
- DESC：降序
### 例子
- 如果对多个字段进行排序时，如果第一个字段可以排出想要的结果，则忽略其他字段，如果不能，则遵守第二个字段，依次类推。

1. 首先按照年龄默认排序：

```mysql
mysql> SELECT * FROM users ORDER BY age;
```

查看表中记录：




2. 插入一条同age数据，当有同龄的用户，我们可以设置如果同龄，则按照id降序排列：

```mysql
mysql> INSERT users VALUES (DEFAULT,'shanfeng','123456',23,1);
mysql> SELECT * FROM users ORDER BY age,id DESC;
```

查看表中记录：

![对结果排序](http://p7vxw6hv7.bkt.clouddn.com/18-5-30/20323468.jpg)

## 语句限制查询数量--LIMIT...
### 语法

```mysql
[LIMIT {[offset,] row_count | row_count OFFSET offset}]
```

### 例子
- 比如查询前两条记录:

```mysql
mysql> SELECT * FROM users LIMIT 2;
```

- 查询第3和4两条记录：
值得注意的是，SELECT语句记录是从0开始，所以想查询3和4两条记录，不能写LIMIT 3,2, 而需要写LIMIT 2,2；

```mysql
mysql> SELECT * FROM users LIMIT 2,2;
```

查看表中记录：

![限制查询数量](http://p7vxw6hv7.bkt.clouddn.com/18-5-30/54843287.jpg)

# 第五章：子查询与连接 

## 数据准备

1. 创建并打开数据库

```mysql
CREATE DATABASE study01;
USE study01;
```

2. 创建数据表

```mysql
  CREATE TABLE IF NOT EXISTS tdb_goods(
    goods_id    SMALLINT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
    goods_name  VARCHAR(150) NOT NULL,
    goods_cate  VARCHAR(40)  NOT NULL,
    brand_name  VARCHAR(40)  NOT NULL,
    goods_price DECIMAL(15,3) UNSIGNED NOT NULL DEFAULT 0,
    is_show     BOOLEAN NOT NULL DEFAULT 1,
    is_saleoff  BOOLEAN NOT NULL DEFAULT 0
  );
```

3. 插入记录

```mysql
 INSERT tdb_goods (goods_name,goods_cate,brand_name,goods_price,is_show,is_saleoff) 
VALUES('R510VC 15.6英寸笔记本','笔记本','华硕','3399',DEFAULT,DEFAULT);
  
 INSERT tdb_goods (goods_name,goods_cate,brand_name,goods_price,is_show,is_saleoff) 
VALUES('Y400N 14.0英寸笔记本电脑','笔记本','联想','4899',DEFAULT,DEFAULT);
  
 INSERT tdb_goods (goods_name,goods_cate,brand_name,goods_price,is_show,is_saleoff) 
VALUES('G150TH 15.6英寸游戏本','游戏本','雷神','8499',DEFAULT,DEFAULT);
  
 INSERT tdb_goods (goods_name,goods_cate,brand_name,goods_price,is_show,is_saleoff) 
VALUES('X550CC 15.6英寸笔记本','笔记本','华硕','2799',DEFAULT,DEFAULT);
  
 INSERT tdb_goods (goods_name,goods_cate,brand_name,goods_price,is_show,is_saleoff)
 VALUES('X240(20ALA0EYCD) 12.5英寸超极本','超级本','联想','4999',DEFAULT,DEFAULT);
  
 INSERT tdb_goods (goods_name,goods_cate,brand_name,goods_price,is_show,is_saleoff) 
VALUES('U330P 13.3英寸超极本','超级本','联想','4299',DEFAULT,DEFAULT);
  
 INSERT tdb_goods (goods_name,goods_cate,brand_name,goods_price,is_show,is_saleoff) 
VALUES('SVP13226SCB 13.3英寸触控超极本','超级本','索尼','7999',DEFAULT,DEFAULT);
  
 INSERT tdb_goods (goods_name,goods_cate,brand_name,goods_price,is_show,is_saleoff) 
VALUES('iPad mini MD531CH/A 7.9英寸平板电脑','平板电脑','苹果','1998',DEFAULT,DEFAULT);
  
 INSERT tdb_goods (goods_name,goods_cate,brand_name,goods_price,is_show,is_saleoff)
 VALUES('iPad Air MD788CH/A 9.7英寸平板电脑 （16G WiFi版）','平板电脑','苹果','3388',DEFAULT,DEFAULT);
  
 INSERT tdb_goods (goods_name,goods_cate,brand_name,goods_price,is_show,is_saleoff) 
VALUES(' iPad mini ME279CH/A 配备 Retina 显示屏 7.9英寸平板电脑 （16G WiFi版）','平板电脑','苹果','2788',DEFAULT,DEFAULT);
  
 INSERT tdb_goods (goods_name,goods_cate,brand_name,goods_price,is_show,is_saleoff) 
VALUES('IdeaCentre C340 20英寸一体电脑 ','台式机','联想','3499',DEFAULT,DEFAULT);
  
 INSERT tdb_goods (goods_name,goods_cate,brand_name,goods_price,is_show,is_saleoff) 

VALUES('Vostro 3800-R1206 台式电脑','台式机','戴尔','2899',DEFAULT,DEFAULT);
  
 INSERT tdb_goods (goods_name,goods_cate,brand_name,goods_price,is_show,is_saleoff)
VALUES('iMac ME086CH/A 21.5英寸一体电脑','台式机','苹果','9188',DEFAULT,DEFAULT);
  
 INSERT tdb_goods (goods_name,goods_cate,brand_name,goods_price,is_show,is_saleoff) 
VALUES('AT7-7414LP 台式电脑 （i5-3450四核 4G 500G 2G独显 DVD 键鼠 Linux ）','台式机','宏碁','3699',DEFAULT,DEFAULT);
  
 INSERT tdb_goods (goods_name,goods_cate,brand_name,goods_price,is_show,is_saleoff) 
VALUES('Z220SFF F4F06PA工作站','服务器/工作站','惠普','4288',DEFAULT,DEFAULT);
  
 INSERT tdb_goods (goods_name,goods_cate,brand_name,goods_price,is_show,is_saleoff) 
VALUES('PowerEdge T110 II服务器','服务器/工作站','戴尔','5388',DEFAULT,DEFAULT);
  
 INSERT tdb_goods (goods_name,goods_cate,brand_name,goods_price,is_show,is_saleoff) 
VALUES('Mac Pro MD878CH/A 专业级台式电脑','服务器/工作站','苹果','28888',DEFAULT,DEFAULT);
  
 INSERT tdb_goods (goods_name,goods_cate,brand_name,goods_price,is_show,is_saleoff) 
VALUES(' HMZ-T3W 头戴显示设备','笔记本配件','索尼','6999',DEFAULT,DEFAULT);
  
 INSERT tdb_goods (goods_name,goods_cate,brand_name,goods_price,is_show,is_saleoff) 
VALUES('商务双肩背包','笔记本配件','索尼','99',DEFAULT,DEFAULT);
  
 INSERT tdb_goods (goods_name,goods_cate,brand_name,goods_price,is_show,is_saleoff) 
VALUES('X3250 M4机架式服务器 2583i14','服务器/工作站','IBM','6888',DEFAULT,DEFAULT);
  
 INSERT tdb_goods (goods_name,goods_cate,brand_name,goods_price,is_show,is_saleoff) 
VALUES('玄龙精英版 笔记本散热器','笔记本配件','九州风神','',DEFAULT,DEFAULT);
  
 INSERT tdb_goods (goods_name,goods_cate,brand_name,goods_price,is_show,is_saleoff) 
VALUES(' HMZ-T3W 头戴显示设备','笔记本配件','索尼','6999',DEFAULT,DEFAULT);
  
 INSERT tdb_goods (goods_name,goods_cate,brand_name,goods_price,is_show,is_saleoff) 
VALUES('商务双肩背包','笔记本配件','索尼','99',DEFAULT,DEFAULT);
```

|字段|解释|
|--|--|
|goods_id|商品id，主键，自动编号
|goods_name|商品名称，禁止为空
|goods_cate|商品分类
|brand_name|品牌名称
|goods_price|商品价格
|is_show|是否上架，布尔类型，默认为1上架，0为下架
|is_saleoff|是否已经全部销售，默认为0可以继续销售

## 子查询简介
- 子查询（Subquery）是指出现在其他SQL语句内的SELECT子句。
- 子查询返回的结果可以返回标量、一行、一列或子查询

```mysql
SELECT * FROM t1 WHERE column1 = (SELECT column1 FROM t2);
```

- SELECT * FROM t1 WHERE column1称为Outer Query[外查询]
- SELECT column1 FROM t2 称为Sub Query[子查询]
- 子查询必须出现在圆括号内。
- 子查询可以包含多个关键字或者条件，比如DISTINCT、GROUP BY、 ORDER BY，LIMIT，函数等。
- 子查询返回的结果可以返回标量、一行、一列或子查询

## 由比较运算符引发的子查询

## 由[NOT] IN/EXISTS引发的子查询

## 使用INSERT...SELECT插入记录

## 多表更新

## 多表更新之一步到位

## 连接的语法结构

## 内连接INNER JOIN

## 外连接OUTER JOIN

## 多表连接

## 关于连接的几点说明

## 无限级分类表设计

## 多表删除








参考文章
[慕课网：《与MySQL的零距离接触》笔记目录](https://zhangjia.tv/682.html)
[MySQL 为什么需要一个主键](https://ruby-china.org/topics/26352) 
[慕课网与MySQL的零距离接触](https://www.imooc.com/learn/122)