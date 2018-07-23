---
title: 剑指offer-42
date: 2018-07-22 19:14:09
tags: 剑指offer
categories: 简单算法
---
# 题目：连续子数组的最大和
输入一个整型数组，数组里有正数也有负数。数组中一个或连续的多个整数组成一个子数组。求所有子数组的和的最大值。要求时间复杂度为`O(n)`。

<!-- more -->

## 举例说明

例如输入的数组为`{1, -2, 3, 10, -4, 7, 2, -5}`，和最大的子数组为`3, 10, -4, 7, 2}`。因此输出为该子数组的和`18 `。

## 思路

### 一. 三层循环（暴力）
求一个数组的最大子数组和，最直观最野蛮的办法便是，三个for循环三层遍历，求出数组中每一个子数组的和，最终求出这些子数组的最大的一个值。 令currSum[i, …, j]为数组A中第i个元素到第j个元素的和（其中0 <= i <= j < n），maxSum为最终求到的最大连续子数组的和。

```
public class _42 {
	static int MaxSubArray(int[] arr) {
		int maxSum = arr[0]; // 全负情况，返回最大负数
		int currSum = 0;
		for (int i = 0; i < arr.length; i++) {
			for (int j = i; j < arr.length; j++) {
				for (int k = i; k <= j; k++) {
					currSum += arr[k];
				}
				if (currSum > maxSum) {
					maxSum = currSum;
				}
				currSum = 0; // 这里要记得清零，否则的话sum最终存放的是所有子数组的和。
			}
		}
		return maxSum;
	}

	public static void main(String[] args) {
		int[] data = { 1, -2, 3, 10, -4, 7, 2, -5 };
		System.out.println(MaxSubArray(data));
	}
}
```

![连续子数组的最大和](http://p7vxw6hv7.bkt.clouddn.com/18-7-22/22976035.jpg)

### 二. 动态规划

事实上，当我们令curSum为当前最大子数组的和，maxSum为最后要返回的最大子数组的和
1. (继承前人遗产吗) 当我们往后扫描时，`对第j+1个元素有两种选择`——要么放入前面找到的子数组，要么做为新子数组的第一个元素：
1.1 如果`currSum+当前元素a[j] >= a[j]`，则令currSum加上a[j]
1.2 否则currSum重新赋值，置为下一个元素，即currSum = a[j]。
2. (更新历代最强吗) 比较当前最大子数组和与最大子数组的和：
2.1 同时，当currSum > maxSum，则更新maxSum = currSum
2.2 否则保持原值，不更新。

即
`curSum = max(a[j], curSum + a[j])`
`maxSum = max(maxSum, curSum)`


举个例子，当输入数组是1, -2, 3, 10, -4, 7, 2, -5，那么，curSum和maxSum相应的变化为：
curSum ：  ` 0 | 1 -1 3 13 9 16 18 13`
maxSum ： `1 | 1 1  3 13 13 16 18 18`

# 代码实现

```java
package JianZhiOfferJava;

public class _42{
    public static int findGreatestSumOfSubArray(int[] arr) {
        if (arr == null || arr.length < 1) {
            throw new IllegalArgumentException("Array must contain an element");
        }     
        int curSum = 0;
        int maxSum = arr[0];
        for (int i =0;i<arr.length;i++) {
            curSum = (arr[i] > curSum + arr[i]) ? arr[i] : curSum + arr[i];//1---------
            maxSum = (maxSum > curSum) ? maxSum : curSum;//2---------
        }
        return maxSum;
    }
          
    public static void main(String[] args) {
        int[] data = {1, -2, 3, 10, -4, 7, 2, -5};
        System.out.println(findGreatestSumOfSubArray(data));
    }
}
```

![连续子数组的最大和](http://p7vxw6hv7.bkt.clouddn.com/18-7-22/22976035.jpg)