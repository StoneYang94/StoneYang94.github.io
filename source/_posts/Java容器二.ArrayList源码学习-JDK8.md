---
title: Java容器二.ArrayList源码学习-JDK8
date: 2018-06-08 20:17:19
tags: Java容器
categories: Java容器
---
按照从构造方法->常用API（增、删、改、查）的顺序来阅读源码，并做简要分析。

<!-- more -->

# 一. 定义

```java
public class ArrayList<E> extends AbstractList<E>
implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```

## 介绍
- 继承了AbstractList，实现了List
它是一个数组队列，提供了相关的添加、删除、修改、遍历等功能
- 实现了RandmoAccess接口
即提供了随机访问功能
- 实现了Cloneable接口
即覆盖了函数clone()，能被克隆
- 实现java.io.Serializable接口
支持序列化，能通过序列化去传输

## 优势
- 比数组的优势
ArrayList 是一个数组队列，相当于动态数组。与Java中的数组相比，它的容量能**动态增长**
- 比Vector的优势
和Vector不同，ArrayList中的操作不是线程安全的，但是开销小，在单线程环境性能优于Vector

# 二 .属性
底层用数组来保存数据

```java
private transient Object[] elementData;
private int size;
```

- size ：当前数组长度
- elementData：存放数组的对象的数组
- modCount：ArrayList集合的修改次数

基于数组实现，保存元素的数组使用 `transient` 修饰，该关键字声明数组默认不会被序列化。这是 ArrayList 具有动态扩容特性，因此保存元素的数组不一定都会被使用，那么就没必要全部进行序列化。**ArrayList 重写了 writeObject() 和 readObject() 来控制只序列化数组中有元素填充那部分内容**。

# 三. 构造函数

##  1）无参构造函数--`ArrayList()`
> Constructs an empty list with an initial capacity of ten.

```java
public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
```



```java
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
```

> Any empty ArrayList with elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA will be expanded to DEFAULT_CAPACITY when the first element is added.

```java
private static final int DEFAULT_CAPACITY = 10;
```

此时我们创建的ArrayList对象中的elementData中的长度是1，size是0,当进行第一次add的时候，elementData将会变成默认的长度：10.(具体过程看下文的add())

## 2)带容量大小的构造函数--`ArrayList(int initialCapacity)`
传入参数如果是大于等于0，则**使用用户的参数初始化**，如果用户传入的参数小于0，则抛出异常
> Constructs an empty list with the specified initial capacity.

```java
public ArrayList(int initialCapacity) {
    if (initialCapacity > 0) {
        this.elementData = new Object[initialCapacity];
    } else if (initialCapacity == 0) {
        this.elementData = EMPTY_ELEMENTDATA;
    } else {
        throw new IllegalArgumentException("Illegal Capacity: "+
                                           initialCapacity);
    }
}
```

## 3)带Collection对象的构造函数--`ArrayList(Collection<? extends E> c)`
构造一个包含指定元素的list，这些元素的是按照Collection的迭代器返回的顺序排列的
1. 将collection对象转换成数组，然后将数组的地址的赋给elementData
2. 更新size的值，同时判断size的大小
2.1 如果是size等于0，直接将空对象EMPTY_ELEMENTDATA的地址赋给elementData
2.2 如果size的值大于0，则执行Arrays.copy方法，把collection对象的内容copy到elementData中
> Constructs a list containing the elements of the specified collection, in the order they are returned by the collection's iterator.

```java
public ArrayList(Collection<? extends E> c) {
    elementData = c.toArray();
    //因为size代表的是集合元素数量，所以通过别的集合来构造ArrayList时，要给size赋值
    if ((size = elementData.length) != 0) {
        //这里是当c.toArray出错，没有返回Object[]时
        //利用Arrays.copyOf 来复制集合c中的元素到elementData数组中
        if (elementData.getClass() != Object[].class)
            elementData = Arrays.copyOf(elementData, size, Object[].class);
    } else {
        // replace with empty array.
        this.elementData = EMPTY_ELEMENTDATA;
    }
}
```

```java
private static final Object[] EMPTY_ELEMENTDATA = {};
```

### Arrays.copyOf()方法

```
   public static <T> T[] copyOf(T[] original, int newLength) {
        return (T[]) copyOf(original, newLength, original.getClass());
    }
 
    public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
        T[] copy = ((Object)newType == (Object)Object[].class)
            ? (T[]) new Object[newLength]
            : (T[]) Array.newInstance(newType.getComponentType(), newLength);
        System.arraycopy(original, 0, copy, 0,
                         Math.min(original.length, newLength));
        return copy;
    }
```

可以看到，他是在方法内部，新建了一个相同类型的数组，然后调用系统方法

```java
System.arraycopy(original, 0, copy, 0,Math.min(original.length, newLength));
```

original - 源数组。 
0 - 源数组中的起始位置。 
copy - 目标数组。 
0 - 目标数据中的起始位置。 
Math.min(original.length, newLength) - 要复制的数组元素的数量。

# 四. 增加---add  addAll
添加元素时使用 `ensureCapacity()` 方法来保证容量足够，如果不够时，需要使用 `grow()` 方法进行扩容，使得新容量为旧容量的 1.5 倍`oldCapacity + (oldCapacity >> 1) `
**扩容操作需要把原数组整个复制到新数组中**，因此最好在创建 ArrayList 对象时就指定大概的容量大小，减少扩容操作的次数。

## 添加一个元素(在末尾)--`add(E e)`

### add(E e)

> Appends the specified element to the end of this list.

```java
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;//在数组末尾追加一个元素，并修改size
    return true;
}
```

### ensureCapacityInternal(int minCapacity) 

- 确保添加的元素有地方存储
- 当第一次添加元素的时候this.size+1 的值是1，所以第一次添加的时候会将当前elementData数组的长度变为10

```java
    private void ensureCapacityInternal(int minCapacity) {
        ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
    }
```

### calculateCapacity(Object[] elementData, int minCapacity)

```java
private static int calculateCapacity(Object[] elementData, int minCapacity) {
    //利用 == 可以判断数组是否是用默认构造函数初始化的
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        return Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    return minCapacity;
}
```

```java
private static final int DEFAULT_CAPACITY = 10;
```

### ensureExplicitCapacity()
经过前面的一顿操作，minCapacity 是
- DEFAULT_CAPACITY（10）
当第一次添加时
- size + 1
当不是第一次添加时

```java
private void ensureExplicitCapacity(int minCapacity) {
    modCount++; //如果确定要扩容，会修改modCount 
    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}
```

### grow()
> Increases the capacity to ensure that it can hold at least the number of elements specified by the minimum capacity argument

```java
private void grow(int minCapacity) {
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1); // 旧容量的1.5倍
  //如果还不够 ，那么就用 能容纳的最小的数量。（add后的容量）
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

## 在指定位置添加一个元素--`add(int index, E element)`
>  Inserts the specified element at the specified position in this list.

1. 确保有足够的容量之后
2. 使用System.arraycopy 将需要插入的位置（index）后面的元素统统往后移动一位
3. 将新的数据内容存放到数组的指定位置（index）上

```java
public void add(int index, E element) {
    rangeCheckForAdd(index);
    // 进行扩容检查
    ensureCapacityInternal(size + 1);  // Increments modCount!!
   // 对数组进行复制处理，目的就是空出index的位置插入element，并将index后的元素位移一个位置
    System.arraycopy(elementData, index, elementData, index + 1,
                     size - index);
   // 将指定的index位置赋值为element
    elementData[index] = element;
    size++;
}
```

### 关于arraycopy()

```java
public static void (Object src,int srcPos,
                    Object dest,int destPos,
                    int length)
```

|参数|意义|
|---|---|
|src|源数组|
|srcPos|源数组要复制的起始位置
|dest|目的数组
|destPos|目的数组放置的起始位置
|length|复制的长度

- src和dest都必须是同类型或者可以进行转换类型的数组
- 可以实现自己到自己复制

### 其中rangeCheckForAdd是为了防止越界：

```java
private void rangeCheckForAdd(int index) {
     if (index > size || index < 0)
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}
```

## 增加一个集合--`addAll(Collection<? extends E> c)`
>   Appends all of the elements in the specified collection to the end of this list, in the order that they are returned by the specified collection's Iterator.

```java
 public boolean addAll(Collection<? extends E> c) {
    Object[] a = c.toArray();//将集合转换为数组
    int numNew = a.length;
    //扩容检查
    ensureCapacityInternal(size + numNew);  // Increments modCount
    //将c添加至list的数据尾部
    System.arraycopy(a, 0, elementData, size, numNew);
    //更新当前容器大小
    size += numNew;
    return numNew != 0;
}
```

## 在指定位置，增加一个集合元素--`addAll(int index, Collection<? extends E> c)`
> Inserts all of the elements in the specified collection into this list, starting at the specified position.

```java
public boolean addAll(int index, Collection<? extends E> c) {
    rangeCheckForAdd(index);
    Object[] a = c.toArray();
    int numNew = a.length;
    ensureCapacityInternal(size + numNew);  // Increments modCount
    //计算需要移动的长度（index之后的元素个数）
    int numMoved = size - index;
    //将数组index后的元素向右移动numNum个位置，即空出第index到index+numNum的位置
    if (numMoved > 0)
        System.arraycopy(elementData, index, elementData, index + numNew,
                         numMoved);
    //将要插入的集合元素复制到数组空出的位置中
    System.arraycopy(a, 0, elementData, index, numNew);
    size += numNew;
    return numNew != 0;
}
```

关于增 的总结： 
add、addAll。 
先判断是否越界，是否需要扩容。 
如果扩容， 就复制数组。 
然后设置对应下标元素值。

值得注意的是： 
1 如果需要扩容的话，默认扩容一半。如果扩容一半不够，就用目标的size作为扩容后的容量。 
2 在扩容成功后，会修改modCount

# 五. 删除---remove

## 根据索引位置删除元素--`remove(int index)`

> Removes the element at the specified position in this list.

```java
public E remove(int index) {
    rangeCheck(index);
    modCount++;
    E oldValue = elementData(index);// 供返回
    int numMoved = size - index - 1;// 计算数组要复制的数量
   // 数组复制，就是将index之后的元素往前移动一个位置
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);
    // 因为删除了一个元素，然后index后面的元素都向前移动了，所以最后一个就没用了
    elementData[--size] = null; // clear to let GC do its work
    return oldValue;
}
```

## 根据元素内容删除，只删除匹配的第一个--`remove(Object o)`

>  Removes the first occurrence of the specified element from this list, if it is present.  If the list does not contain the element, it is unchanged.

循环遍历所有对象，得到对象所在索引位置，然后调用fastRemove方法，执行remove操作

-  null值要用==比较

```java
    public boolean remove(Object o) {
        if (o == null) {
            for (int index = 0; index < size; index++)
                if (elementData[index] == null) {
                    fastRemove(index);
                    return true;
                }
        } else {
            for (int index = 0; index < size; index++)
                if (o.equals(elementData[index])) {
                    fastRemove(index);
                    return true;
                }
        }
        return false;
    }
```

### fastRemove()
> Private remove method that skips bounds checking and does not return the value removed.

定位到需要remove的元素索引，先将index后面的元素往前面移动一位（调用System.arraycooy实现），然后将最后一个元素置空。

```java
private void fastRemove(int index) {
    modCount++;
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);
    elementData[--size] = null; // clear to let GC do its work
}
```

###  数组越界检查

```java
private void rangeCheck(int index) {
    if (index >= size)
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}
```

```java
private String outOfBoundsMsg(int index) {
    return "Index: "+index+", Size: "+size;
}
```

# 六. 查找---get
## 查找指定位置上的元素
> Returns the element at the specified position in this list.

```java
public E get( int index) {
    RangeCheck(index);
    return (E) elementData [index];
}
```

# 七. 更新---set
## 将指定位置的元素更新为新元素
>Replaces the element at the specified position in this list with the specified element.

- 注意返回值是oldValue

```java
public E set(int index, E element) {
    rangeCheck(index);
    E oldValue = elementData(index);// 记录要更新位置的元素，供返回使用
    elementData[index] = element;
    return oldValue;
}
```

# 八. 判断

##  包含--`contains(Object o) `
调用indexOf方法，遍历数组中的每一个元素作对比，如果找到对于的元素，则返回true，没有找到则返回false

```java
public boolean contains(Object o) {
    return indexOf(o) >= 0;
}
```

```java
public int indexOf(Object o) {
    if (o == null) {
        for (int i = 0; i < size; i++)
            if (elementData[i]==null)
                return i;
    } else {
        for (int i = 0; i < size; i++)
            if (o.equals(elementData[i]))
                return i;
    }
    return -1;
}
```

## 空--`isEmpty()`

```java
public boolean isEmpty() {
    return size == 0;
}
```

# 九. ArrayList遍历方式
ArrayList支持3种遍历方式
1. 通过迭代器遍历。即通过Iterator去遍历。

```java
Integer value = null;
Iterator iter = list.iterator();
while (iter.hasNext()) {
    value = (Integer)iter.next();
}
```

2. 随机访问，通过索引值去遍历。
由于ArrayList实现了RandomAccess接口，它支持通过索引值去随机访问元素。

```java
Integer value = null;
int size = list.size();
for (int i=0; i<size; i++) {
    value = (Integer)list.get(i);        
}
```

3. for循环遍历。如下：

```java
Integer value = null;
for (Integer integ:list) {
    value = integ;
}
```

# 十. ArrayList和 Vector 的区别
- 同步
Vector 和 ArrayList 几乎是完全相同的，唯一的区别在于 Vector 是同步的，因此开销就比 ArrayList 要大，访问速度更慢。最好使用 ArrayList 而不是 Vector，因为同步操作完全可以由程序员自己来控制；
- 扩容
Vector 每次扩容请求其大小的 2 倍空间，而 ArrayList 是 1.5 倍。
Vector 的grow()扩容部分

```java
 int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                 capacityIncrement : oldCapacity);
```

# 十一. 小回顾
- 添加元素时可能要扩容（所以最好预判一下），删除元素时不会减少容量（若希望减少容量，trimToSize()），删除元素时，将删除掉的位置元素置为null，下次gc就会回收这些元素所占的内存空间
- add(int index, E element)：添加元素到数组中指定位置的时候，需要将该位置及其后边所有的元素都整块向后复制一位
- get(int index)：获取指定位置上的元素时，可以通过索引直接获取（O(1)）
- remove(Object o)需要遍历数组
- remove(int index)不需要遍历数组，只需判断index是否符合条件即可，效率比remove(Object o)高
- contains(E)需要遍历数组


参考文章
[Java集合干货系列-（一）ArrayList源码解析](https://www.jianshu.com/p/2cd7be850540)
[Java官方文档](https://docs.oracle.com/javase/8/docs/api/)
[ArrayList源码解析（JDK8）](https://blog.csdn.net/zxt0601/article/details/77281231)