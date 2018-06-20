---
title: HashMap源码学习-基于JDK8
date: 2018-06-13 23:34:01
tags:
- Java集合
- 源码
categories: Java集合
---

# 1. 定义

```java
public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable 
```

<!-- more -->

- 实现了Cloneable接口
即覆盖了函数clone()，能被克隆
- 实现java.io.Serializable接口
支持序列化，能通过序列化去传输

# 2. 介绍
## 结构实现
HashMap是数组+链表+红黑树（JDK1.8增加了红黑树部分）实现的，当链表长度达到8，会转化成红黑树，以提升它的查询、插入效率
![image](http://upload-images.jianshu.io/upload_images/11861611-e84d6bf379936981.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 设计思路
- Map<K,V>
Map<K,V>是一种以键值对存储数据的容器，而HashMap则是借助了键值Key的hashcode值来组织存储，使得可以非常快速和高效地地根据键值key进行数据的存取
- Entry<Key,Value>
对于键值对<Key,Value>，HashMap内部会将其封装成一个对应的Entry<Key,Value>对象，即Entry<Key,Value>对象是键值对<Key,Value>的组织形式
- 存
对于每个对象而言，JVM都会为其生成一个hashcode值。HashMap在存储键值对Entry<Key,Value>的时候，会根据**Key的hashcode值**，以某种映射关系，决定应当将这对键值对Entry<Key,Value>存储在HashMap中的什么位置上
- 取
当通过Key值取数据的时候，然后根据Key值的hashcode，以及内部映射条件，直接定位到Key对应的Value值存放在什么位置，可以非常高效地将Value值取出。

---

- 线程不安全的
- 允许key为null,value为null
- 遍历时无序 
其底层数据结构是数组称之为哈希桶，每个桶里面放的是链表，链表中的每个节点，就是哈希表中的每个元素。 

因其底层哈希桶的数据结构是数组，所以也会涉及到扩容的问题。

当HashMap的容量达到threshold域值时，就会触发扩容。扩容前后，哈希桶的长度一定会是2的次方。 
这样在根据key的hash值寻找对应的哈希桶时，可以用位运算替代取余操作，更加高效。

而key的hash值，并不仅仅只是key对象的hashCode()方法的返回值，还会经过扰动函数的扰动，以使hash值更加均衡。 
因为hashCode()是int类型，取值范围是40多亿，只要哈希函数映射的比较均匀松散，碰撞几率是很小的。 
但就算原本的hashCode()取得很好，每个key的hashCode()不同，但是由于HashMap的哈希桶的长度远比hash取值范围小，默认是16，所以当对hash值以桶的长度取余，以找到存放该key的桶的下标时，由于取余是通过与操作完成的，会忽略hash值的高位。因此只有hashCode()的低位参加运算，发生不同的hash值，但是得到的index相同的情况的几率会大大增加，这种情况称之为hash碰撞。 即，碰撞率会增大。

扰动函数就是为了解决hash碰撞的。它会综合hash值高位和低位的特征，并存放在低位，因此在与运算时，相当于高低位一起参与了运算，以减少hash碰撞的概率。（在JDK8之前，扰动函数会扰动四次，JDK8简化了这个操作）

扩容操作时，会new一个新的Node数组作为哈希桶，然后将原哈希表中的所有数据(Node节点)移动到新的哈希桶中，相当于对原哈希表中所有的数据重新做了一个put操作。所以性能消耗很大，可想而知，在哈希表的容量越大时，性能消耗越明显。

扩容时，如果发生过哈希碰撞，节点数小于8个。则要根据链表上每个节点的哈希值，依次放入新哈希桶对应下标位置。 
因为扩容是容量翻倍，所以原链表上的每个节点，现在可能存放在原来的下标，即low位， 或者扩容后的下标，即high位。 high位= low位+原哈希桶容量 
如果追加节点后，链表数量》=8，则转化为红黑树

由迭代器的实现可以看出，遍历HashMap时，顺序是按照哈希桶从低到高，链表从前往后，依次遍历的。属于无序集合。


# 3 .属性
## DEFAULT_INITIAL_CAPACITY 
初始容量，必须是2的倍数，默认是16   (1 << 4)

```java
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16  
```

## MAXIMUM_CAPACITY 
最大所能容纳的key-value 个数，最大值是 1073 741 824(1 << 30)
  
```java
static final int MAXIMUM_CAPACITY = 1 << 30;  
```

## DEFAULT_LOAD_FACTOR
默认的加载因子  

```java
static final float DEFAULT_LOAD_FACTOR = 0.75f;
```

## TREEIFY_THRESHOLD
树化链表节点的阈值，当某个链表的长度大于或者等于这个长度，则扩大数组容量，或者数化链表  

```java
static final int TREEIFY_THRESHOLD = 8;  
```

## Node<K,V>
Node<K,V> 类是HashMap中的静态内部类，实现Map.Entry<K,V>接口。 定义了key键、value值、next节点，也就是说元素之间构成了单向链表

```java
static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;//哈希值
        final K key;
        V value;
        Node<K,V> next;

        Node(int hash, K key, V value, Node<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }

        public final K getKey()        { return key; }
        public final V getValue()      { return value; }
        public final String toString() { return key + "=" + value; }

        //每一个节点的hash值，是将key的hashCode 和 value的hashCode 异或得到的
        public final int hashCode() {
            return Objects.hashCode(key) ^ Objects.hashCode(value);
        }
        //设置新的value 同时返回旧value
        public final V setValue(V newValue) {
            V oldValue = value;
            value = newValue;
            return oldValue;
        }

        public final boolean equals(Object o) {
            if (o == this)
                return true;
            if (o instanceof Map.Entry) {
                Map.Entry<?,?> e = (Map.Entry<?,?>)o;
                if (Objects.equals(key, e.getKey()) &&
                    Objects.equals(value, e.getValue()))
                    return true;
            }
            return false;
        }
}
```

##   Node<K,V>[] table
存储数据的Node数组，长度是2的幂
>   The table, initialized on first use, and resized as necessary. When allocated, length is always a power of two.

```java
transient Node<K,V>[] table;  
```

至此，HashMap存储的数据结构也就很清晰了：维护了一个数组，每个数组又维护了一个单向链表。之所以这么设计，考虑到遇到哈希冲突的时候，同index的value值就用单向链表来维护。

## size
map中保存的键值对的数量  

```java
transient int size;  
```
  
## threshold
> The next size value at which to resize (capacity * load factor).

- 容量*装载因子
- threshold是HashMap判断size是否需要扩容的阈值：如果key-value的数量等于该值，则调用resize方法，扩大容量（2倍），同时修改threshold的值

```java  
int threshold;  
```

  ## loadFactor
装载因子  

```java
final float loadFactor;  
```


# 4. 构造函数

##  无参构造函数
### HashMap() 
> Constructs an empty {@code HashMap} with the default initial capacity

```java
public HashMap() {  
    this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted  
}  
```

### DEFAULT_LOAD_FACTOR

```java
static final float DEFAULT_LOAD_FACTOR = 0.75f;
```

## 指定初始化容量的构造函数

### HashMap(int initialCapacity) 
> Constructs an empty {@code HashMap} with the specified initial capacity and the default load factor (0.75).

并不是指定的初始容量是多少，初始化之后的HashMap的容量就是多大，tableSizeFor（）方法会**把初始化的容量变成是2的次方数**

```java 
public HashMap(int initialCapacity) {
    this(initialCapacity, DEFAULT_LOAD_FACTOR);
}
```

##  指定初始化容量 以及 加载因子

### HashMap(int initialCapacity, float loadFactor) 

```java
    public HashMap(int initialCapacity, float loadFactor) {
        //边界处理
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal initial capacity: " +
                                               initialCapacity);
        //初始容量最大不能超过2的30次方
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
        //加载因子不能为负数
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal load factor: " +
                                               loadFactor);
        this.loadFactor = loadFactor;
        //设置阈值   1.>= 初始化容量    2. 是2的n次方
        this.threshold = tableSizeFor(initialCapacity);
    }
```

### tableSizeFor(int cap)
- 根据期望容量cap，返回**2的n次方形式的 **哈希桶的实际容量 length
- 返回值一般会大于等于cap 

```java
static final int tableSizeFor(int cap) {
  //经过下面的 或 和位移 运算， n最终各位都是1。
    int n = cap - 1;
    n |= n >>> 1;
    n |= n >>> 2;
    n |= n >>> 4;
    n |= n >>> 8;
    n |= n >>> 16;
    //判断n是否越界，返回 2的n次方作为 table（哈希桶）的阈值
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}
```

1. 给定的cap减1

```java
int n = cap - 1
```

如果cap本来就是2的幂次方，经过后续的未操作的，cap将会变成2 * cap，是不符合我们预期的

2. n无符号右移1位

```java
n |= n >>> 1 
```

- 即n二进制最高位的1右移一位，导致的结果是n二进制的高2位值为1
- 目前n的高1~2位均为1

3.  n继续无符号右移2位

```java
n |= n >>> 2 
```

- 导致n二进制表示高3~4位经过运算值均为1
- 目前n的高1~4位均为1

4. n继续无符号右移4位

```java
n |= n >>> 4
```

- 导致n二进制表示高5~8位经过运算值均为1；
- 目前n的高1~8位均为1。

5. n继续无符号右移8位。

```java
n |= n >>> 8
```

- n | (n >>> 8)，导致n二进制表示高9~16位经过运算值均为1
- 目前n的高1~16位均为1

6.  n继续无符号右移16位。

```java
n |= n >>> 16 
```

- 导致n二进制表示高17~32位经过运算值均为1
- 目前n的高1~32位均为1


可以看出，无论给定cap(cap < MAXIMUM_CAPACITY )的值是多少，经过以上运算，其值的二进制所有位都会是1。再将其加1，这时候这个值一定是2的幂次方。当然如果经过运算值大于MAXIMUM_CAPACITY，直接选用MAXIMUM_CAPACITY。
这里可以举个栗子，假设给定的cap的值为20。

int n = cap - 1; —>  n = 19(二进制表示：0001 0011)

n |= n >>> 1;

```
    n             ->  0001 0011
    n >>> 1       ->  0000 1001
    n |= n >>> 1  ->  0001 1011
```

n |= n >>> 2;

```
    n             ->  0001 1011
    n >>> 2       ->  0000 1101
    n |= n >>> 2  ->  0001 1111此时n所有位均为1，后续的位操作均不再改变n的值。
```

...

```
    n + 1        ->  0010 0000 (32)
```

最终，tableSizeFor(20)的结果为32(2^5)。
至此tableSizeFor保证cap为2的幂次方

### 为什么cap要保持为2的幂次方？
- index怎么算
HashMap中存储数据table的index是由key的Hash值决定的
- Hash怎么算
在JDK1.8中，HashMap中key的Hash值由Hash(key)方法（后面会详细分析）计算得来
在HashMap存储数据的时候，我们期望数据能够均匀分布，以避免哈希冲突。自然而然我们就会想到去用%取余的操作来实现我们这一构想
- 优化
取余(%)操作中如果**除数是2的幂次方**则等同于与其除数减一的与(&)操作

```java
 index = e.hash & (newCap - 1) 

 等同于：

 index = e.hash % newCap
```

采用二进制位操作&，相对于%，能够提高运算效率

##    参数是map 的构造函数
将另一个map m 里的所有元素加入表中
### HashMap(Map<? extends K, ? extends V> m)

```java
public HashMap(Map<? extends K, ? extends V> m) {
    this.loadFactor = DEFAULT_LOAD_FACTOR;
    putMapEntries(m, false);
}
```

###  putMapEntries(Map<? extends K, ? extends V> m, boolean evict) 
- 将另一个Map的所有元素加入表中，参数evict初始化时为false，其他情况为true
- 用get()  put() 

1. 判断表是否为空
1.1 为空表，计算阈值
1.2 非空表，判断是否需要扩容
2. 遍历 m 依次将元素加入当前表中

```java
final void putMapEntries(Map<? extends K, ? extends V> m, boolean evict) {
    int s = m.size();//m的元素数量
    if (s > 0) {//元素数量大于0
        //如果当前表是空的
        if (table == null) { // pre-size
            //根据m的元素数量和当前表的加载因子，计算出阈值
            float ft = ((float)s / loadFactor) + 1.0F;
            //修正阈值的边界 
            //不能超过MAXIMUM_CAPACITY
            int t = ((ft < (float)MAXIMUM_CAPACITY) ?
                     (int)ft : MAXIMUM_CAPACITY);
            //如果新的阈值大于当前阈值
            if (t > threshold)
                //返回一个>=新的阈值且满足2的n次方的阈值
                threshold = tableSizeFor(t);
        }
        //如果当前元素表不是空的
        else if (s > threshold)
            resize();
        //遍历 m 依次将元素加入当前表中
        for (Map.Entry<? extends K, ? extends V> e : m.entrySet()) {
            K key = e.getKey();
            V value = e.getValue();
            putVal(hash(key), key, value, false, evict);
        }
    }
}
```


### resize()

#### 时机
当前的HashMap的大小大于阀值时，HashMap会对此HashMap的容量进行扩充，即对内部的Entry[] table 数组进行扩充

#### 要求
HashMap对容量（Entry[] table数组长度） 有两点要求：
1. 容量的大小应当是 2的N次幂
2. 当容量大小超过阀值时，容量扩充为当前的一倍

#### 步骤
容量扩充分为以下几个步骤：
1. 确定新的阈值和容量
1.1 旧容量>0
没有超过最大容量，则新表容量、门限为旧表2倍
1.2 旧容量=0，旧门限>0
旧表门限值赋值给新表容量，新表阈值=容量 * 负载因子
1.3 旧容量=0，旧门限=0
则新的容量和门限为默认的容量（16）和门限值（12）
2. 将当前哈希桶中的所有节点转移到新的哈希桶中
旧的链表不空，且链表中有元素
2.1 链表中就一个元素（没有发生哈希碰撞）
直接将这个元素放置在新的哈希桶里
2.2 发生过哈希碰撞 ,且节点数超过8个
转化成了红黑树
2.3 发生过哈希碰撞，节点数小于8个
    - 根据链表上每个节点的哈希值，依次放入新哈希桶对应下标位置
    - 因为扩容是容量翻倍，原链表上的每个节点，现在可能存放在原来的下标low位； 或者扩容后的下标high位
    - high位=  low位+原哈希桶容量

```java
final Node<K,V>[] resize() {
        Node<K,V>[] oldTab = table; 
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        int oldThr = threshold;
        int newCap, newThr = 0;
        if (oldCap > 0) { //1.1 -------------------------------------------
            if (oldCap >= MAXIMUM_CAPACITY) { //扩容前的数组大小如果已经达到最大(2^30)了
                //修改阈值为int的最大值(2^31-1)，这样以后就不会扩容了
                threshold = Integer.MAX_VALUE;  
                return oldTab;//返回当前哈希桶，不再扩容
            }
            // 没超过最大值，就扩充为原来的2倍
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
                newThr = oldThr << 1; // double threshold
        }//表是空的，但有阈值。代表是初始化时指定了容量、阈值的情况
        else if (oldThr > 0) // 1.2 -----------------------
            newCap = oldThr;
        else {// 1.3----------------------------------------
            newCap = DEFAULT_INITIAL_CAPACITY;
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        }
        if (newThr == 0) {
            float ft = (float)newCap * loadFactor;//根据新表容量 和 加载因子 求出新的阈值
            //进行越界修复
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
        threshold = newThr;
        @SuppressWarnings({"rawtypes","unchecked"})
            Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        table = newTab;      //更新哈希桶引用
      //2.-----------------------------------------
        if (oldTab != null) {
            for (int j = 0; j < oldCap; ++j) {
                Node<K,V> e; 
                if ((e = oldTab[j]) != null) {//如果当前桶中有元素,则将链表赋值给e
                    oldTab[j] = null;//将原哈希桶置空以便GC
                    if (e.next == null) //2.1--------------------
                        newTab[e.hash & (newCap - 1)] = e;
                    else if (e instanceof TreeNode)//2.2-----------------------
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    else { //2.3------------------------------------
                        Node<K,V> loHead = null, loTail = null;
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
                        do {
                            next = e.next;
                            //原索引
                            if ((e.hash & oldCap) == 0) {
                                //给头尾节点指针赋值
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }//高位索引
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }//循环直到链表结束
                        } while ((e = next) != null);
                        //将低位链表存放在原index处，
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                        //将高位链表存放在新index处
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
        return newTab;
    }
```

# 5. 增加---put
## 添加一个元素

### put(K key, V value)
>  Associates the specified value with the specified key in this map.If the map previously contained a mapping for the key, the old value is replaced.

```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}
```

### hash(K,V) 方法
>  Computes key.hashCode() and spreads (XORs) higher bits of hash to lower.

- HashMap中table的index是由Key的哈希值决定的
- 而上面我们提到index的运算规则是e.hash & (newCap - 1)。由于newCap是2的幂次方，那么newCap - 1的高位应该全部为0。如果e.hash值只用自身的hashcode的话，那么index只会和e.hash低位做&操作。这样一来，index的值就只有低位参与运算，高位毫无存在感，从而会增加哈希冲突的风险
- HashMap并没有直接使用key的hashcode()，在计算key的哈希值的时候，用其自身hashcode值与其低16位做异或操作

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

。

### putVal(int hash, K key, V value, boolean onlyIfAbsent,boolean evict)

>  @param 
hash： hash for key
 key ：the key
 value： the value to put
onlyIfAbsent： if true, don't change existing value
 evict： if false, the table is in creation mode.
 return：previous value, or null if none

流程图和步骤参考自[美团点评](https://tech.meituan.com/java-hashmap.html)
对步骤语序逻辑有所调整

#### 流程图

![流程图](http://upload-images.jianshu.io/upload_images/11861611-58c4c0ed8eba9775.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 步骤

1. 判断键值对数组table[i]是否为空或为null
如果当前哈希表是空的，代表是初始化，则执行resize()进行扩容
2. 根据键值key计算hash值得到插入的数组索引i
    - 如果table[i]==null，直接新建节点添加（index 是利用 哈希值 & 哈希桶的长度-1，替代模运算）转向⑥
    - 如果table[i]不为空，转向③
3. 发生了哈希冲突
判断table[i]的首个元素是否和key一样，相同指的是hashCode以及equals
    - 如果相同直接覆盖value
    - 否则转向④
4. 判断table[i] 是否为treeNode，即table[i] 是否是红黑树
    - 如果是红黑树，则直接在树中插入键值对
    - 否则转向⑤
5. 遍历table[i]，判断链表长度是否大于8
    - 大于8的话把链表转换为红黑树，在红黑树中执行插入操作
    - 否则进行链表的插入操作；遍历过程中若发现key已经存在直接覆盖value即可
6. 插入成功后，判断实际存在的键值对数量size是否超多了最大容量threshold，如果超过，进行扩容

#### 源码
在构造函数中最多也只是设置了initialCapacity、loadFactor的值，并没有初始化table，table的初始化工作是在put方法中进行的

```java
inal V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
      //1.----------------------------------------空表？
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
      //2.----------------------------------------数组索引
        if ((p = tab[i = (n - 1) & hash]) == null)
      //3.----------------------------------------
            tab[i] = newNode(hash, key, value, null);
        else {
            Node<K,V> e; K k;
            //如果哈希值相等，key也相等，则是覆盖value操作
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
       //4.----------------------------------------
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
       //5.----------------------------------------
                for (int binCount = 0;  ; ++binCount) {
                    if ((e = p.next) == null) {//遍历到尾部，追加新节点到尾部
                        p.next = newNode(hash, key, value, null);
                        //如果追加节点后，链表数量>=8，则转化为红黑树
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                      // key相同则跳出循环
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            //如果e不是null，说明有需要覆盖的节点
            if (e != null) { // existing mapping for key
                //则覆盖节点值，并返回原oldValue
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                //这是一个空实现的函数，用作LinkedHashMap重写使用
                afterNodeAccess(e);
                return oldValue;
            }
        }
        //6.------------------------------------
        ++modCount;
        if (++size > threshold)
            resize();
        //这是一个空实现的函数，用作LinkedHashMap重写使用
        afterNodeInsertion(evict);
        return null;
    }
```

# 6. 删除---remove

## remove(key)


执行逻辑：
1）根据key得到key的hash值
2）根据key 和hash值定位需要remove的Node
3)  将Node从对应的链表移除，然后再将Node 前后的节点对接起来
4）返回被移除 的Node
5）key-value的数量减一，修改次数加一



## 




# 小总结： 
* 运算尽量都用位运算代替，更高效 
* 对于扩容导致需要新建数组存放更多元素时，除了要将老数组中的元素迁移过来，也记得将老数组中的引用置null，以便GC 
* 取下标 是用 哈希值 与运算 （桶的长度-1） i = (n - 1) & hash。 由于桶的长度是2的n次方，这么做其实是等于 一个模运算。但是效率更高 
* 扩容时，如果发生过哈希碰撞，节点数小于8个。则要根据链表上每个节点的哈希值，依次放入新哈希桶对应下标位置
* 因为扩容是容量翻倍，所以原链表上的每个节点，现在可能存放在原来的下标，即low位， 或者扩容后的下标，即high位。 high位= low位+原哈希桶容量 
* 利用哈希值 与运算 旧的容量 ，if ((e.hash & oldCap) == 0),可以得到哈希值去模后，是大于等于oldCap还是小于oldCap，等于0代表小于oldCap，应该存放在低位，否则存放在高位。这里又是一个利用位运算 代替常规运算的高效点 
* 如果追加节点后，链表数量》=8，则转化为红黑树 
* 插入节点操作时，有一些空实现的函数，用作LinkedHashMap重写使用。




参考文章
[Java集合干货系列-（三）HashMap源码解析](https://www.jianshu.com/p/e6536af1018f)
[Java官方文档](https://docs.oracle.com/javase/8/docs/api/)
[美团技术团队Java 8系列之重新认识HashMap](https://tech.meituan.com/java-hashmap.html)
[源码分析之 HashMap](https://juejin.im/post/58f2f47061ff4b0058f4b7cc)
[[Java基础要义] HashMap的设计原理和实现分析](https://blog.csdn.net/luanlouis/article/details/41576373)