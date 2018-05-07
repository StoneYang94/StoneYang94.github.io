---
title: 剑指offer_03
date: 2018-05-07 23:32:07
tags: 剑指offer
categories: 简单算法
---
# 题目
在一个长度为n的数组里的所有数字都在0到n-1的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。
<!-- more -->
# 举例说明
例如，如果输入长度为7的数组{2,3,1,0,2,5,3}，那么对应的输出是重复的数字2或者3。
# 思路
1. **排序**
解决这个问题的一个简单的方法是先把输入的数组排序。从排序的数组中找出重复的数字时间很容易的事情，只需要从头到尾扫描排序后的数组就可以了。排序一个长度为n的数组需要**O(nlogn)的时间**。
2. **hash 空间换时间**
将每个数映射到哈希表，扫描一遍能统计出所有数字出现的次数。然后扫描哈希表能在O(1)的时间内得到该数据出现的次数。这样便能找到重复的数字了。这个算法的时间复杂度是O(n)，但它提高时间效率是以一个大小为O(n)的哈希表为代价的。我们再看看有没有空间复杂度为O(1)的算法。 
3. **寻找数组规律**
思想是这样的：顺序扫描数组的每个数字。当扫描到下标为i的数字时，比较该数字M是不是与下标i相等，如果相等，扫描下一个；如不等，再把它M和下标为M的数字相比较，如果相等，则找到一个返回；如果不等，则交换它们的值。(参考文章和用图[《剑指offer》:[51]数组中的重复数字](https://blog.csdn.net/gogokongyin/article/details/51775734))
以数组a[7]={2,3,1,0,2,5,3}为例，分析如下图所示：
![过程](http://upload-images.jianshu.io/upload_images/11861611-c42f2c720e5bbcc2.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
###  代码

```java
public class _03 {
	public static int duplicate(int[] number) {
		if (number == null || number.length < 1) {
			return -1;
		}
		for (int i : number) {
			if (i < 0 || i >= number.length) {
				return -1;
			}
		}

		for (int i = 0; i < number.length; i++) {
			// 当number[i]与i不相同的时候一直交换
			while (number[i] != i) {
				// 如果i位置与number[i]位置的数字相同，说明有重复数字
				if (number[i] == number[number[i]]) {
					return number[i];
				}
				// 如果不同就交换
				else {
					swap(number, i, number[i]);
				}
			}
		}
		return -1;
	}

	private static void swap(int[] data, int x, int y) {
		int tmp = data[x];
		data[x] = data[y];
		data[y] = tmp;
	}
	
	public static void main(String[] args) {
		int[] numbers1 = { 3, 2, 3, 4, 2 };//功能测试 
		System.out.println(duplicate(numbers1));    //3

		int[] numbers2 = { -3, 2, 2, 4, 2 };//有负数
		System.out.println(duplicate(numbers2));    //-1

		int[] numbers3 = { 2, 1, 1, 6, 2 };//正数超过范围
		System.out.println(duplicate(numbers3));    //-1

		int[] numbers4 = { 1, 2, 3, 4, 0 };//在范围内，但没重复
		System.out.println(duplicate(numbers4));    //-1

		int[] numbers5 = { 1, 1, 1, 1, 1 };//在范围内，全部重复
		System.out.println(duplicate(numbers5));    //-1
}
```

### 输出

![测试用例输出](http://upload-images.jianshu.io/upload_images/11861611-276add4370881523.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)