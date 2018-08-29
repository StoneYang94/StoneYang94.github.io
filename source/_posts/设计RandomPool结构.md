---
title: 设计RandomPool结构
date: 2018-08-29 19:51:05
tags: 左神算法课
categories: 左神算法课
---
# 题目（算法课第六课）

## 要求
设计一种结构，在该结构中有如下三个功能：
- insert(key)：
将某个key加入到该结构，做到不重复加入
- delete(key)：
将原本在结构中的某个key移除
- getRandom()：
`等概率随机返回`结构中的任何一个key

Insert()、delete()和getRandom()方法的时间复杂度都是`O(1)`

<!-- more -->

# 分析

## 用两个hash表

为了得到`key -> index`以及`index -> key`的对应关系，使用两个哈希表来建立两者之间的映射。

![两个hash表](http://upload-images.jianshu.io/upload_images/11861611-b8416f0f75a7c311.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- map1中存放的 key value 分别是 相应的值  和对应的插入顺序
- map2中正好相反， key value 分别是 对应的插入顺序和 相应的值

## insert(key)
对每个元素都加上一个下标，第一个元素的下标为0，每加入一个元素，对应的下标加1。

## getRandom()
就是利用size，使用`Math.random() * size` 产生一个随机数，范围就是[0, size )的一个值，然后从map2中获取相应的数字对应的值即可

## delete(key)

### 错误示范
如果直接在map1和map2中进行删除操作的话，会产生很多空缺的地方，此时，如果getRandom()的话，产生的随机数的位置很可能是空的，这样就不能保证O(1)的时间复杂度
会产生一个个的`洞`

### 正确的做法是
将最后一个`覆盖`到相应的删除位置,用最后的记录去填补 被删的记录。
若删除的是其中位置x处的值，则
1. 把map1中倒数第一个value赋为删除处的value（即为插入元素的次序）
2. 把map2中应该删除的次序的位置的value也赋成倒数第一个的值
3. **删除对应位置上的**值


# 代码实现

```java
import java.util.HashMap;

public class RandomPool{
	public static class Pool<K> {
		private HashMap<K, Integer> keyIndexMap;
		private HashMap<Integer, K> indexKeyMap;
		private int size;

		public Pool() {
			this.keyIndexMap = new HashMap<K, Integer>();
			this.indexKeyMap = new HashMap<Integer, K>();
			this.size = 0;
		}

		public void insert(K key) {
			if (!this.keyIndexMap.containsKey(key)) {
				this.keyIndexMap.put(key, this.size);
				this.indexKeyMap.put(this.size++, key);
			}
		}

		public K getRandom() {
			if (this.size == 0) {
				return null;
			}
			int randomIndex = (int) (Math.random() * this.size); // 0 ~ size -1
			return this.indexKeyMap.get(randomIndex);//真正的随机了
		}

		public void delete(K key) {
			if (this.keyIndexMap.containsKey(key)) {
				int deleteIndex = this.keyIndexMap.get(key);//返回被删除处的index
      //maps2 的最后一个位置的索引，找到map2最后的 键
				int lastIndex = --this.size;
				K lastKey = this.indexKeyMap.get(lastIndex);
       //被删除的位置的值，变成了，原本最后的key 的value
				this.keyIndexMap.put(lastKey, deleteIndex);
				this.indexKeyMap.put(deleteIndex, lastKey);
      // 移除最后的位置
				this.keyIndexMap.remove(key);
				this.indexKeyMap.remove(lastIndex);
			}
		}
	}

	public static void main(String[] args) {
		Pool<String> pool = new Pool<String>();
		pool.insert("datiangou");
		pool.insert("cimu");
		pool.insert("quanyecha");
		System.out.println(pool.getRandom());
		System.out.println(pool.getRandom());
		System.out.println(pool.getRandom());
		System.out.println(pool.getRandom());
		System.out.println(pool.getRandom());
		System.out.println(pool.getRandom());
	}
}
```

输出：

```
datiangou
quanyecha
datiangou
cimu
cimu
quanyecha
```
