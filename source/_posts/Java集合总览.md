---
title: Java集合总览
date: 2018-06-08 20:16:50
tags: Java集合
categories: Java集合
---
# 一. 概览
容器主要包括 Collection 和 Map 两种，Collection 又包含了 List、Set 以及 Queue。

<!-- more -->
![绿色表示接口；红色表示常用的类](http://upload-images.jianshu.io/upload_images/11861611-a8bfa90b89587dd5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 基本概念
Java容器类类库的用途是持有对象
1. Collection
一个独立元素的序列，这些元素都服从一条或多条规则。其中List必须按照插入的顺序保存元素、Set不能有重复的元素、Queue按照排队规则来确定对象的产生顺序（通常也是和插入顺序相同）
2. Map
一组成对的值键对对象，允许用键来查找值。Map允许我们使用一个对象来查找某个对象
## Collection
###  List
- ArrayList：**基于动态数组**实现，支持随机访问
- LinkedList：**基于双向循环链表**实现，只能顺序访问，但是可以快速地在链表中间插入和删除元素。不仅如此，LinkedList 还可以用作栈、队列和双端队列
-  Vector：和 ArrayList 类似，但它是线程安全的

### Set
- HashSet：基于哈希实现，支持快速查找，但不支持有序性操作，例如根据一个范围查找元素的操作。并且失去了元素的插入顺序信息，也就是说**使用 Iterator 遍历 HashSet 得到的结果是不确定的**
- TreeSet：基于红黑树实现，支持有序性操作，但是**查找效率不如 HashSet**，HashSet 查找时间复杂度为 O(1)，TreeSet 则为 O(logn)
- LinkedHashSet：具有 HashSet 的查找效率，且内部使用链表维护元素的插入顺序。

### Queue
- LinkedList：可以用它来支持**双向队列**
- PriorityQueue ：是基于堆结构实现，可以用它来实现**优先级队列**

## Map
- HashMap：基于哈希实现
- HashTable：和 HashMap 类似，但它是线程安全的，这意味着同一时刻多个线程可以同时写入 HashTable 并且不会导致数据不一致。它是遗留类，不应该去使用它。现在可以使用 ConcurrentHashMap 来支持线程安全，并且 ConcurrentHashMap 的效率会更高，因为 ConcurrentHashMap 引入了分段锁
- LinkedHashMap：使用链表来维护元素的顺序，顺序为插入顺序或者最近最少使用（LRU）顺序
- TreeMap：基于红黑树实现

