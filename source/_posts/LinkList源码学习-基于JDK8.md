---
title: LinkList源码学习-基于JDK8
date: 2018-06-11 19:41:04
tags:
- Java集合
- 源码
categories: Java集合
---

# 1. 定义

```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
```
<!-- more -->
- 实现 List 接口
能对它进行队列操作
- 实现 Deque 接口
即能将LinkedList当作双端队列使用
- 实现了Cloneable接口
即覆盖了函数clone()，能克隆
- 实现java.io.Serializable接口
支持序列化，能通过序列化去传输



# 2. 属性

主要有三个：
- size：当前有多少个节点
- first：第一个节点
- last：最后一个节点

```java
transient int size = 0;  
transient Node<E> first;  
transient Node<E> last;  
```

# 3. 构造方法

## 无参构造方法

```java
public LinkedList() {
}
```

什么都没做，表示初始化的时候size为0，first和last的节点都为空：

## Collection对象作为入参

```java
public LinkedList(Collection<? extends E> c) {
    this();
    addAll(c);
}
```

### addAll(Collection<? extends E> c) ---在尾部批量增加
>  Appends all of the elements in the specified collection to the end of this list

```java
public boolean addAll(Collection<? extends E> c) {
    return addAll(size, c);
}
```

### addAll(int index, Collection<? extends E> c) ---在index插入集合c中所有元素
> Inserts all of the elements in the specified collection into this list, starting at the specified position.  Shifts the element currently at that position (if any) and any subsequent elements to the right (increases their indices). 

1. 判断加在队尾还是中间（为了找到插入后的前驱后继节点，在插入前的位置）
2. 循环加入元素
2.1从前驱节点进行后接
2.2设置为前驱的后继
2.3当前节点设置为下一节点的前驱-
3. 判断加在队尾还是中间（为了判断last位置，和设置新的前驱后继）

```java
public boolean addAll(int index, Collection<? extends E> c) {
    checkPositionIndex(index);// [0,size]
    Object[] a = c.toArray();
    int numNew = a.length;//新增元素的数量
    if (numNew == 0)
        return false;
    //successor and predecessor---------------------------
    //维护前、后链接
    Node<E> pred, succ;
    if (index == size) {//加在尾部
        succ = null;//size节点（队尾）的后置节点一定是null
        pred = last;//前置节点是队尾
    } else {//加在指定位置idx处
        succ = node(index);//插入前的index节点，在插入后，是被插入节点（们）的后置节点
        pred = succ.prev;//插入后的前置接点，是插入前index节点的前一个节点
    }
     //for循环遍历原数组，依次执行插入节点操作，对链表批量增加
    for (Object o : a) {
        @SuppressWarnings("unchecked") E e = (E) o;
        //之前的最后一个节点是现在的前驱节点
    //1. 从前驱节点进行后接----------------
        Node<E> newNode = new Node<>(pred, e, null);
    //2. 设置为前驱的后继----------------------------
        if (pred == null)//如果前置节点是空，说明是头结点
            first = newNode;
        else//否则 新节点是前置节点的后置节点
            pred.next = newNode;
    //3. 当前节点设置为下一节点的前驱----------------
        pred = newNode;//当前的节点为前置节点
    }
    //循环结束后，判断
    if (succ == null) {//在队尾添加的
        last = pred; //last节点发生变化
    } else {// 否则是在队中插入的节点
        pred.next = succ; //接尾
        succ.prev = pred;//接头
    }
    size += numNew;
    modCount++;
    return true;
}
```

### 范围判定
```java
private void checkPositionIndex(int index) {
    if (!isPositionIndex(index))
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}
```

```java
private boolean isPositionIndex(int index) {
    return index >= 0 && index <= size;
}
```


### Node<E>---双向链表。

```java
private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;

    Node(Node<E> prev, E element, Node<E> next) {
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}
```

###  node(int index) ---根据index 查询出Node
> Returns the (non-null) Node at the specified element index.

```java
Node<E> node(int index) {
    // assert isElementIndex(index);
    if (index < (size >> 1)) {//在前半段
        Node<E> x = first;
        for (int i = 0; i < index; i++)
            x = x.next;
        return x;
    } else {//在后半段
        Node<E> x = last;
        for (int i = size - 1; i > index; i--)
            x = x.prev;
        return x;
    }
}
```

# 4. 增加---add  addAll

## 添加一个元素(在末尾)

###  add(E e)
> Appends the specified element to the end of this list.

```java
public boolean add(E e) {
    linkLast(e);
    return true;
}
```

### linkLast(E e)
> Links e as last element.

```java
void linkLast(E e) {
    final Node<E> l = last;
    //1. 以原尾部节点为新节点的前置节点------------
    final Node<E> newNode = new Node<>(l, e, null);
    //2.更新尾部节点-------------------
    last = newNode;
    if (l == null)//若原链表为空链表，更新头结点
        first = newNode;
    else//3. 否则更新原尾节点的后继节点为新节点---------------
        l.next = newNode;
    size++;
    modCount++;
}
```

## 在指定位置添加一个元素
### add(int index, E element)

```java
public void add(int index, E element) {
    checkPositionIndex(index);//[0,size]
    if (index == size)//在尾节点后插入
        linkLast(element);
    else//在中间插入
        linkBefore(element, node(index));
}
```

### linkBefore(E e, Node<E> succ)
> Inserts element e before non-null Node succ.

```java
void linkBefore(E e, Node<E> succ) {
    // assert succ != null;
    //修改前驱后继---------
    final Node<E> pred = succ.prev;
    final Node<E> newNode = new Node<>(pred, e, succ);
    //插在succ前
    succ.prev = newNode;
    if (pred == null)//如果pred是空，说明succ是原头结点，新节点是现在的头结点
        first = newNode;
    else//否则新节点是pred的后继节点
        pred.next = newNode;
    size++;
    modCount++;
}
```

# 5. 删除---remove
- 按下标删，是先根据index找到Node，然后去链表上unlink掉这个Node
- 按元素删，会先去遍历链表寻找是否有该Node，考虑到允许null值，所以会遍历两遍，然后再去unlink它

## 无参数
### remove()

```java
public E remove() {
    return removeFirst();
}
```

### removeFirst()

```java
public E removeFirst() {
    final Node<E> f = first;
    if (f == null)
        throw new NoSuchElementException();
    return unlinkFirst(f);
}
```

### unlinkFirst(f)

```java
private E unlinkFirst(Node<E> f) {
    // assert f == first && f != null;
    final E element = f.item;
    final Node<E> next = f.next;
    f.item = null;
    f.next = null; // help GC
    first = next;
    if (next == null)
        last = null;
    else
        next.prev = null;
    size--;
    modCount++;
    return element;
}
```

## 根据索引位置删除元素
### remove(int index)
> Removes the element at the specified position in this list

```java
public E remove(int index) {
    checkElementIndex(index);//检查是否越界 下标[0,size)
    return unlink(node(index));//从链表上删除某节点
}
```

### unlink(Node<E> x)

```java
E unlink(Node<E> x) {
    // assert x != null;
    final E element = x.item; 
    final Node<E> next = x.next; 
    final Node<E> prev = x.prev;

    if (prev == null) { //如果前驱节点为空，说明当x原本是头结点
        first = next; 
    } else { //改后连接
        prev.next = next;
        x.prev = null; 
    }

    if (next == null) {//如果后继节点为空，说明x原本是尾节点
        last = prev; 
    } else {//改前连接
        next.prev = prev;
        x.next = null;
    }

    x.item = null; 
    size--; 
    modCount++;  
    return element; 
}
```

## 根据元素内容删除，只删除匹配的第一个
### remove(Object o) 
> Removes the first occurrence of the specified element from this list, if it is present.

-  null值要用==比较

```java
public boolean remove(Object o) {
    if (o == null) {//如果要删除的是null节点
        for (Node<E> x = first; x != null; x = x.next) {
            if (x.item == null) {
                unlink(x);
                return true;
            }
        }
    } else {
        for (Node<E> x = first; x != null; x = x.next) {
            if (o.equals(x.item)) {
                unlink(x);
                return true;
            }
        }
    }
    return false;
}
```

# 6. 查找---get
## 查询节点---get(int index) 
> Returns the element at the specified position in this list.

```java
public E get(int index) {
    checkElementIndex(index);
    return node(index).item;
}
```

## 查询下标---indexOf(Object o)

```java
public int indexOf(Object o) {
    int index = 0;
    if (o == null) {
        for (Node<E> x = first; x != null; x = x.next) {
            if (x.item == null)
                return index;
            index++;
        }
    } else {
        for (Node<E> x = first; x != null; x = x.next) {
            if (o.equals(x.item))
                return index;
            index++;
        }
    }
    return -1;
}
```

# 7. 更新---set
## 将指定位置的元素更新为新元素
>Replaces the element at the specified position in this list with the specified element.

- 注意返回值是oldValue

```java
public E set(int index, E element) {
    checkElementIndex(index); 
    Node<E> x = node(index);
    E oldVal = x.item;//保存旧值 供返回
    x.item = element;//覆盖旧值
    return oldVal;
}
```

# 9.  toArray()
new 一个新数组 然后遍历链表，将每个元素存在数组里，返回

>Returns an array containing all of the elements in this list in proper sequence (from first to last element).

```java
public Object[] toArray() {
     Object[] result = new Object[size];
     int i = 0;
     for (Node<E> x = first; x != null; x = x.next)
         result[i++] = x.item;
     return result;
}
```

# 10. LinkedList和 ArrayList的区别
- 增删效率比ArrayList高
底层数据结构是链表，增删只需要移动指针即可故时间效率较高。不需要批量扩容，也不需要预留空间，所以空间效率比ArrayList高。
- 查效率比ArrayList低
缺点就是需要随机访问元素时，时间效率很低，虽然底层在根据下标查询Node的时候，会根据index判断目标Node在前半段还是后半段，然后决定是顺序还是逆序查询，以提升时间效率。总体时间效率依然O(n)

# 11. 小总结
它的CRUD操作里，都涉及到根据index去找到Node的操作
- 链表批量增加，是靠for循环遍历原数组，依次执行插入节点操作
对比ArrayList是通过System.arraycopy完成批量增加的
- 通过下标获取某个node 的时候，会根据index处于前半段还是后半段进行一个折半，以提升查询效率
- 按下标删是先根据index找到Node，然后去链表上unlink掉这个Node
按元素删，会先去遍历链表寻找是否有该Node，如果有，去链表上unlink掉这个Node
- 改也是先根据index找到Node，然后替换值。改不修改modCount。
- 查本身就是根据index找到Node。


参考文章
[Java集合干货系列-（二）LinkedList源码解析](https://www.jianshu.com/p/d5ec2ff72b33)
[Java官方文档](https://docs.oracle.com/javase/8/docs/api/)