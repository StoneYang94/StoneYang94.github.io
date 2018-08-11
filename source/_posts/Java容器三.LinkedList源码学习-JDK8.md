---
title: Java容器三.LinkedList源码学习-JDK8
date: 2018-06-11 19:41:04
tags: Java容器
categories: Java容器
---
按照从构造方法->常用API（增、删、改、查）的顺序来阅读源码，并会讲解阅读方法中涉及的一些变量的意义。

<!-- more -->

- ArrayList与LinkedList
ArrayList与LinkedList是List接口的两种不同的实现，ArrayList的增删效率低，但是改查效率高。 
而LinkedList正好相反，增删由于不需要移动底层数组数据，其底层是链表实现的，只需要修改链表节点指针，所以效率较高。 
而改和查，都需要先定位到目标节点，所以效率较低。
- Collection.toArray(); 。 
这个方法很重要，不管是ArrayList、LinkedList 在批量add的时候，都会先转化成数组去做。 因为数组可以用for循环直接花式遍历。比较方便 高效

# 一. 定义

```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
```

- 实现 List 接口
能对它进行队列操作
- 实现 Deque 接口
即能将LinkedList当作双端队列使用
- 实现了Cloneable接口
即覆盖了函数clone()，能克隆
- 实现java.io.Serializable接口
支持序列化，能通过序列化去传输



# 二. 属性

## 主要有三个：
- size：当前有多少个节点
- first：第一个节点
- last：最后一个节点

```java
transient int size = 0;  
transient Node<E> first;  
transient Node<E> last;  
```

## Node<E>
双向链表。

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

# 三. 构造方法

## 1）无参构造方法

```java
public LinkedList() {
}
```

什么都没做，表示初始化的时候size为0，first和last的节点都为空：

## 2）Collection对象作为入参

```java
public LinkedList(Collection<? extends E> c) {
    this();
    addAll(c);
}
```



# 四. 增加---add  addAll

## 添加一个元素(在末尾)--`add(E e)`
在尾部插入一个节点： add

###  add(E e)
> Appends the specified element to the end of this list.

```java
public boolean add(E e) {
    linkLast(e);
    return true;
}
```

### linkLast(E e)
生成新节点 并插入到 链表尾部， 更新 last/first 节点。
只用维护前面的链子

```java
void linkLast(E e) {
    final Node<E> l = last;//记录原尾部节点
    final Node<E> newNode = new Node<>(l, e, null);//以原尾部节点为新节点的前置节点
    last = newNode;//更新尾部节点
    if (l == null)//若原链表为空链表，需要 额外 更新头结点
        first = newNode;
    else//否则更新原尾节点的后置节点为 新节点 
        l.next = newNode;
    size++;
    modCount++;
}
```

## 在指定位置添加一个元素--`add(int index, E element)`

```java
public void add(int index, E element) {
    checkPositionIndex(index);//检查下标是否越界[0,size]
    if (index == size)//在尾节点后插入
        linkLast(element);
    else//在中间插入
        linkBefore(element, node(index));
}
```

### linkBefore(E e, Node<E> succ)
在succ节点前，插入一个新节点e
重新维护前后两条链子

```java
void linkBefore(E e, Node<E> succ) {
    // assert succ != null;
    final Node<E> pred = succ.prev; //保存后置节点的前置节点
    final Node<E> newNode = new Node<>(pred, e, succ); //构建一个新节点
    //插在succ前
    succ.prev = newNode;//新节点newNode是原节点succ的前置节点
    //如果之前的前置节点是空，说明succ是原头结点。所以新节点是现在的头结点
    if (pred == null)
        first = newNode;
    else//否则新节点是pred的后继节点
        pred.next = newNode;
    size++;//修改数量
    modCount++;
}
```

### 在尾部批量增加---`addAll(Collection<? extends E> c)`
>  Appends all of the elements in the specified collection to the end of this list

```java
public boolean addAll(Collection<? extends E> c) {
    return addAll(size, c);//以size为插入下标，插入集合c中所有元素
}
```

###  在尾部批量增加---`addAll(int index, Collection<? extends E> c) `
以size为插入下标，插入集合c中所有元素

1. 判断加在队尾还是中间（为了找到插入后的前驱后继节点，在插入前的位置）
2. 循环加入元素
2.1从前驱节点进行后接
2.2设置为前驱的后继
2.3当前节点设置为下一节点的前驱-
3. 判断加在队尾还是中间（为了判断last位置，和设置新的前驱后继）

```java
public boolean addAll(int index, Collection<? extends E> c) {
    checkPositionIndex(index);//检查越界 [0,size] 闭区间
    Object[] a = c.toArray();//拿到目标集合数组
    int numNew = a.length;
    if (numNew == 0)//如果新增元素数量为0，则不增加，并返回false
        return false;
    //successor and predecessor---------------------------
    Node<E> pred, succ;//index节点的前置节点，后置节点
    if (index == size) { //在链表尾部追加数据
        succ = null;//size节点（队尾）的后置节点一定是null
        pred = last;//前置节点是队尾
    } else {
        succ = node(index);//取出index节点，作为后置节点
        pred = succ.prev;//前置节点是，index节点的前一个节点
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
        pred = newNode;//步进，当前的节点为前置节点了，为下次添加节点做准备
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

### 根据index 查询出Node---`node(int index)`
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

# 五. 删除---remove
- 按下标删，是先根据index找到Node，然后去链表上unlink掉这个Node
- 按元素删，会先去遍历链表寻找是否有该Node，考虑到允许null值，所以会遍历两遍，然后再去unlink它

## 删除头--` remove()`

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

## 根据索引位置删除元素--`remove(int index)`
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
        first = next; //则头结点等于后置节点 
    } else { //----------改后连接
        prev.next = next;
        x.prev = null;  //将当前节点的 前置节点置空
    }

    if (next == null) {//如果后继节点为空，说明x原本是尾节点
        last = prev; //则 尾节点为前置节点
    } else {//---------改前连接
        next.prev = prev;
        x.next = null;//将当前节点的 后置节点置空
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

# 六. 查找---get
## 查询节点---get(int index) 
> Returns the element at the specified position in this list.

```java
public E get(int index) {
    checkElementIndex(index);
    return node(index).item;//调用node()方法 取出 Node节点
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
[LinkedList源码解析（JDK8）](https://blog.csdn.net/zxt0601/article/details/77341098)