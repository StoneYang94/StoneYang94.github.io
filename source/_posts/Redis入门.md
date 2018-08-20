---
title: Redis入门
date: 2018-08-20 23:39:41
tags: Redis入门
categories: 数据库 
---
最近项目中用到Redis实现异步点赞功能。本文是学习笔记性质的博客，以深化理解和方便日后复习。

<!-- more -->

# 简介

## 定义
REmote DIctionary Server(Redis) 是一个key-value存储系统，是非关系型数据库(NoSQL)。
它通常被称为数据结构服务器，因为值（value）可以是 字符串(String), 哈希(Map), 列表(list), 集合(sets) 和 有序集合(sorted sets)等类型。

## 对比

### 与关系数据库
相比于关系数据库，Redis不使用表，它的数据库也没有预定义或者强制的方式来要求用户关联Redis存储的不同数据。

### 与其他 key - value 缓存产品
Redis 与其他 key - value 缓存产品有以下三个特点：
- Redis支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用。
- Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。
- Redis支持数据的备份，即master-slave模式的数据备份。

# 数据类型与常用命令
Redis支持五种数据类型：string（字符串），hash（哈希），list（列表），set（集合）及zset(sorted set：有序集合)

## 1. String（字符串）

### 特点
- string是redis最基本的类型，一个key对应一个value
- string类型是二进制安全的
意思是redis的string可以包含任何数据。比如jpg图片或者序列化的对象 
- 一个键最大能存储512MB

### 常用命令

| 命令 | 描述 |
|---|---|
|SET key value|设置指定 key 的值|
|GET key|获取指定 key 的值 |
|GETRANGE key start end|返回 key 中字符串值的子字符|
|STRLEN key|返回 key 所储存的字符串值的长度|

### 实例 

```redis
redis 127.0.0.1:6379> SET name "w3cschool.cn"
OK
redis 127.0.0.1:6379> GET name
"w3cschool.cn"
```
使用了 Redis 的 SET 和 GET 命令。键为 name，对应的值为w3cschool.cn。

## 2. Hash（哈希）

### 特点
- Redis hash 是一个键值对集合
- Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象
- 每个 hash 可以存储 2<sup>32</sup> - 1 键值对（40多亿）

### 常用命令

| 命令 | 描述 |
|---|---|
|HSET key field value|将哈希表 key 中的字段 field 的值设为 value |
|HMSET key field1 value1 [field2 value2 ]|同时将多个 field-value (域-值)对设置到哈希表 key 中 |
|HGET key field|获取存储在哈希表中指定字段的值|
|HGETALL key|获取在哈希表中指定 key 的所有字段和值|

### 实例

```redis
redis 127.0.0.1:6379> HMSET user:1 username w3cschool.cn password w3cschool.cn points 200
OK
redis 127.0.0.1:6379> HGETALL user:1
1) "username"
2) "w3cschool.cn"
3) "password"
4) "w3cschool.cn"
5) "points"
6) "200"
```

以上实例中 hash 数据类型存储了包含用户脚本信息的用户对象。实例中我们使用了 Redis HMSET, HGETALL 命令，user:1 为键值。

## 3. List（列表）

### 特点
- Redis 列表是简单的**字符串列表**，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）
- 列表最多可存储  2<sup>32</sup> - 1 元素 (40多亿)

### 常用命令

| 命令 | 描述 |
|---|---|
|LPUSH key value1 [value2]|将一个或多个值插入到列表头部|
|LRANGE key start stop|获取列表指定范围内的元素|
|LSET key index value|通过索引设置列表元素的值|
| LPOP key|移出并获取列表的第一个元素 |

### 实例

```redis
redis 127.0.0.1:6379> lpush w3cschool.cn redis
(integer) 1
redis 127.0.0.1:6379> lpush w3cschool.cn mongodb
(integer) 2
redis 127.0.0.1:6379> lpush w3cschool.cn rabitmq
(integer) 3
redis 127.0.0.1:6379> lrange w3cschool.cn 0 10
1) "rabitmq"
2) "mongodb"
3) "redis"
```

## 4. Set（集合）

### 特点
- Redis的Set是string类型的无序集合
- 集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是O(1)
- 集合中最大的成员数为  2<sup>32</sup> - 1 元素 (40多亿)

### 常用命令

| 命令 | 描述 |
|---|---|
|SADD key member1 [member2]|向集合添加一个或多个成员|
|SPOP key|移除并返回集合中的一个随机元素|
|SCARD key|获取集合的成员数 |
|SMEMBERS key|返回集合中的所有成员|

### 实例

```redis
redis 127.0.0.1:6379> sadd w3cschool.cn redis
(integer) 1
redis 127.0.0.1:6379> sadd w3cschool.cn mongodb
(integer) 1
redis 127.0.0.1:6379> sadd w3cschool.cn rabitmq
(integer) 1
redis 127.0.0.1:6379> sadd w3cschool.cn rabitmq
(integer) 0
redis 127.0.0.1:6379> smembers w3cschool.cn

1) "rabitmq"
2) "mongodb"
3) "redis"
```

注意：以上实例中 rabitmq 添加了两次，但根据集合内元素的唯一性，第二次插入的元素将被忽略。

## 5. zset(sorted set：有序集合)

### 特点
- Redis zset 和 set 一样也是string类型元素的集合,且不允许重复的成员
- 不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序
- zset的成员是唯一的,但分数(score)却可以重复

### 常用命令

| 命令 | 描述 |
|---|---|
|ZADD key score1 member1 [score2 member2]|向有序集合添加一个或多个成员，或者更新已存在成员的分数|
|ZCARD key|获取有序集合的成员数|
|ZRANGE key start stop [WITHSCORES]|通过索引区间返回有序集合成指定区间内的成员|
|ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT]|通过分数返回有序集合指定区间内的成员|

### 实例

```redis
redis 127.0.0.1:6379> zadd w3cschool.cn 0 redis
(integer) 1
redis 127.0.0.1:6379> zadd w3cschool.cn 0 mongodb
(integer) 1
redis 127.0.0.1:6379> zadd w3cschool.cn 0 rabitmq
(integer) 1
redis 127.0.0.1:6379> zadd w3cschool.cn 0 rabitmq
(integer) 0
redis 127.0.0.1:6379> ZRANGEBYSCORE w3cschool.cn 0 1000

1) "redis"
2) "mongodb"
3) "rabitmq"
```






