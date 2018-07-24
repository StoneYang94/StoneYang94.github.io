---
title: 剑指offer-49
date: 2018-07-25 00:03:44
tags: 剑指offer
categories: 简单算法
---
# 题目：丑数
我们把只包含因子2、3 和5 的数称作丑数（Ugly Number）。求从小到大的顺序的第1500个丑数。

<!-- more -->

## 举例说明
例如6、8 都是丑数，但14 不是，它包含因子7。习惯上我们把1 当做第一个丑数。

## 思路

### 方法一：逐个判断每个整数是不是丑数的解法，暴力但不够高效

所谓一个数m是另一个数n的因子，是指n能被m整除，也就是说`n%m == 0`
根据丑数的定义，丑数只能被2，3，5整除。也就是说如果一个数能被2整除，我们把它连续除以2；如果能被3整除，就连续除以3；如果能被5整除，就除以5.如果最后我们得到的是1，那么这个数就是丑数，否则不是。

接下来，我们只需要按照顺序判断每个整数是不是丑数，

#### 代码实现

```java
public class _49{	
	public static boolean isUgly(int number){
		while(number % 2 == 0)
			number/=2;
		while(number % 3 == 0)
			number /=3;
		while(number % 5 == 0)
			number /=5;
		return number == 1;
	}
	public static int getUglyNumber(int index){
		if(index <= 0)
			return 0;
		int num= 0;
		int uglyFound = 0;
		while(uglyFound < index){
			num++;
			if(isUgly(num)){
				++uglyFound;
			}
		}
		return num;
	}

    public static void main(String[] args) {
        System.out.println(getUglyNumber(1500)); // 859963392
    }
}
```

![丑数](http://p7vxw6hv7.bkt.clouddn.com/18-7-23/28882854.jpg)

### 方法二：创建数组保存已经找到的丑数，用空间换时间的解法

前面的算法之所以效率低，**很大程度上是因为不管一个数是不是丑数我们对它都要作计算**。接下来我们试着找到一种`只要计算丑数`的方法，而`不在非丑数的整数上花费时间`。

根据丑数的定义，丑数应该是另一个丑数乘以2，3，5的结果。因此我们可以创建一个数组，里面的数字是排序好的丑数，每一个丑数都是前面的丑数乘以2，3，5得到的。

这种思路的关键在于怎样确定数组里面的丑数是排序好的。假设数组中已经有若干个丑数排好后存放在数组中，并且把已有的最大的丑数记作M，我们接下来分析如何生成下一个丑数。该丑数肯定是前面某个丑数乘以2，3，5的结果。所以我们首先考虑把已有的每个丑数乘以2.在乘以2的时候，能得到若干个小于或等于M的结果。由于是按照顺序生成的，小于或者等于M肯定已经在数组中了，我们不需要再次考虑；还会得到若干个大于M的结果，但我们只需要第一个大于M的结果，因为我们希望丑数是指按从小到大的顺序生成的，其他更大的结果以后再说。我们把得到的**第一个乘以2后大于M的结果即为M2**。同样，我们把已有的每一个丑数乘以3，5，能得到**第一个大于M的结果M3和M5**。那么下一个丑数应该是M2,M3,M5这3个数的最小者。

前面分析的时候，提到把已有的每个丑数分别都乘以2，3，5。事实上这不是必须的，因为已有的丑数都是按顺序存放在数组中的。对乘以2而言，肯定存在某一个丑数T2，排在它之前的每一个丑数乘以2得到的结果都会小于已有的最大丑数，在它之后的每一个丑数乘以2得到的结果都会太大。我们只需记下这个丑数的位置，同时每次生成新的丑数的时候，去更新这个T2。对乘以3和5而言，也存在这同样的T3和T5。

例如：当前数组中的丑数是：1，此时2*1，3*1，5*1，中最小的是2，将2存到数组中，此时数组中的数为1，2。Max=2，又因为2*1==Max，因此2乘的丑数应该为2*2，3*1>2,5*1>2,在新的4，3，5中最小的为3将3存到数组中，依次循环。

在求解的过程中，进行从小到大依次求得丑数；
每个丑数都是较小的丑数乘以2、3、5得到的；
分别用三个指针指向需要分别乘以2、3、5的基丑数；
每次取求得的三个丑数中的最小的一个，作为当前要求的丑数。

#### 代码实现

```java
public class _49 {
	public static int getUglyNumber(int index) {
		if (index < 0)
			return 0;
		int urgly[] = new int[index];// 辅助数组
		urgly[0] = 1;
		int min;// min用于保存新产生的大于当前数组最大值的最小丑数
		int nextindex2 = 0, nextindex3 = 0, nextindex5 = 0;
		for(int urglyindex = 1;urglyindex < index;urglyindex++) {
			min = urgly[nextindex2] *2< urgly[nextindex3] *3 ? urgly[nextindex2] *2 : urgly[nextindex3] *3;
			min = min < urgly[nextindex5] *5 ? min : urgly[nextindex5] *5;
			urgly[urglyindex] = min;
			while (urgly[nextindex2] * 2 <= min) {// 如果urgly[nextindex2]小于当前数组的最大值
				nextindex2++;
			}
			while (urgly[nextindex3] * 3 <= min) {// 如果urgly[nextindex3]小于当前数组的最大值
				nextindex3++;
			}
			while (urgly[nextindex5] * 5 <= min) {// 如果urgly[nextindex5]小于当前数组的最大值
				nextindex5++;
			}	
		}
		return urgly[index - 1];
	}

	public static void main(String[] args) {
		System.out.println(getUglyNumber(1500)); // 859963392
	}
}
```

![丑数](http://p7vxw6hv7.bkt.clouddn.com/18-7-23/28882854.jpg)
