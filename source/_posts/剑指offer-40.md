---
title: 剑指offer-40
date: 2018-07-22 19:14:02
tags: 剑指offer
categories: 简单算法
---
# 题目：最小的k个数
输入n个整数，找出其中最小的k个数。

<!-- more -->

## 举例说明
例如输入`4 、5 、1、6、2、7、3 、8` 这8 个数字，则最小的`4 `个数字是`1 、2、3 、4`

## 思路

### 一. 排序法---`时间复杂度O(nlogn)`

要求一个序列中最小的k个数，按照惯有的思维方式，则是先对这个序列从小到大排序，然后输出前面的最小的k个数。
至于选取什么的排序方法，我想你可能会第一时间想到快速排序（我们知道，快速排序平均所费时间为n*logn），然后再遍历序列中前k个元素输出即可。因此，总的时间复杂度：O（n * log n)+O(k)=O（n * log n）。

### 二. 数组---`时间复杂度O(nk)`

咱们再进一步想想，题目没有要求最小的k个数有序，也没要求最后n-k个数有序。既然如此，就没有必要对所有元素进行排序。这时，咱们想到了用选择或交换排序，即：
1、遍历n个数，把最先遍历到的k个数存入到大小为k的数组中，假设它们即是最小的k个数；
2、对这k个数，利用选择或交换排序找到这k个元素中的最大值kmax（找最大值需要遍历这k个数，时间复杂度为O（k））；
3、继续遍历剩余n-k个数。假设每一次遍历到的新的元素的值为x，把x与kmax比较：如果x < kmax ，用x替换kmax，并回到第二步重新找出k个元素的数组中最大元素kmax‘；如果x >= kmax，则继续遍历不更新数组。
每次遍历，更新或不更新数组的所用的时间为O（k）或O（0）。故整趟下来，时间复杂度为n*O（k）=O（n*k）。

### 三. 大根堆---`时间复杂度O(nlogk)`

更好的办法是维护容量为k的最大堆(适合处理海量数据)，原理跟解法二的方法相似：

1. 用容量为k的最大堆存储`最先遍历到的k个数`，同样假设它们即是最小的k个数
2. 堆中元素是有序的，令k1<k2<...<kmax（`kmax`设为最大堆中的最大元素）
3. 遍历剩余n-k个数。假设每一次遍历到的新的元素的值为x，把x与堆顶元素kmax比较：
3.1 如果x < kmax，用x`替换`kmax，然后`更新`堆（用时logk）
3.2 否则不更新堆

这样下来，总的时间复杂度:O（k+（n-k）*logk）=O（n*logk）。此方法得益于堆中进行**查找和更新**的时间复杂度均为：O(logk)（若使用解法二：在数组中找出最大元素，时间复杂度：O（k））。

# 代码实现
大根堆法

```java
public class _40 {
	public static void printTopMinK(int[] arr, int k) {
		if (arr == null || arr.length == 0 || k <= 0)
			return;
		for (int i = 0; i < k; i++) { // 建堆
			heapInsert(arr, i);
		}
		for (int i = k; i < arr.length; i++) { // 循环比较更新堆
			heapify(arr, i, k);
		}
		for (int i = 0; i < k; i++) {// 打印
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	public static void heapInsert(int[] arr, int index) {
		while (arr[index] > arr[(index - 1) / 2]) {// 是否取代父
			swap(arr, index, (index - 1) / 2);
			index = (index - 1) / 2;
		}
	}

	public static void heapify(int[] arr, int index, int size) {
		if (arr[index] > arr[0])
			return;
		swap(arr, 0, index);
	    int cur = 0;
	    int cand;
	    while (cur < size) {
	        cand = cur;
	        if (cur * 2 + 1 < size && arr[cur * 2 + 1] > arr[cand])
	            cand = cur * 2 + 1;
	        if (cur * 2 + 2 < size && arr[cur * 2 + 2] > arr[cand])
	            cand = cur * 2 + 2;
	        if (cand == cur)
	            break;
	        else {
	            swap(arr, cur, cand);
	            cur = cand;
	        }
	    }
	}
	
	public static void swap(int[] arr, int i, int j) {
		int temp = arr[i];
		arr[i] = arr[j];
		arr[j] = temp;
	}

	public static void main(String[] args) {
		int[] arr = new int[] { 1, 9, 2, 8, 3, 7, 4, 6, 5 };
		for (int i = 1; i <= arr.length; i++) {
			printTopMinK(arr, i);
		}
	}
}
```

![最小的k个数](http://upload-images.jianshu.io/upload_images/11861611-6a08996094162b59.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)