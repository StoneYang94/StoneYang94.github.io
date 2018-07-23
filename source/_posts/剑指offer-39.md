---
title: 剑指offer-39
date: 2018-07-22 19:13:55
tags: 剑指offer
categories: 简单算法
---
# 题目：数组中出现次数超过一半的数字
数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字

<!-- more -->

## 举例说明
如输入一个长度为9 的数组｛ 1, 2, 3, 2, 2, 2, 5, 4, 2｝。由于数字2在数组中出现了5 次，超过数组长度的一半，因此输出2 。

## 思路

### 一：排序法（暴力）--`时间复杂度为O(n*logn)`

如果无序，那么我们是不是可以先把数组中所有这些数字先进行排序（至于排序方法可选取最常用的快速排序）。排完序后，直接遍历，在遍历整个数组的同时统计每个数字的出现次数，然后把那个出现次数超过一半的数字直接输出，题目便解答完成了。总的时间复杂度为`O(nlogn + n)`

本题目数据状况：一个数字在数组中的出现次数超过了一半，那么在已排好序的数组索引的N/2处。因此，我们只需要对整个数组排完序之后，然后直接输出数组中的第N/2处的数字即可，这个数字即是整个数组中出现次数超过一半的数字，总的时间复杂度由于少了最后一次整个数组的遍历，`缩小到O(n*logn)`
然时间复杂度并无本质性的改变，我们需要找到一种更为有效的思路或方法。


### 二：hash表---`空间复杂度为O(n)，时间复杂度为O(1)`

既要缩小总的时间复杂度，那么可以用查找时间复杂度为O(1)的hash表，即以空间换时间。哈希表的键值（Key）为数组中的数字，值（Value）为该数字对应的次数。然后直接遍历整个hash表，找出每一个数字在对应的位置处出现的次数，输出那个出现次数超过一半的数字即可。

### 三：“抓乌龟”---`时间复杂度O(n)，空间复杂度O(1)`

Hash表需要O(n)的空间开销，且要设计hash函数，还有没有更好的办法呢？我们可以试着这么考虑，如果`每次删除两个不同的数（不管是不是我们要查找的那个出现次数超过一半的数字）`，那么，**在剩下的数中，我们要查找的数（出现次数超过一半）出现的次数仍然超过总数的一半。通过不断重复这个过程，不断排除掉其它的数，最终找到那个出现次数超过一半的数字。**这个方法，免去了排序，也避免了空间O(n)的开销，总得说来，时间复杂度只有O(n)，空间复杂度为O(1)。
举个简单的例子，如数组`a[5] = {0, 1, 2, 1, 1};`
过程如下简单表示：

```
0 1 2 1 1 =>2 1 1=>1
```

最终1即为所找。

### 五：根据数组组特点(投票算法)---`时间复杂度为O(n)`

更进一步，考虑到这个问题本身的特殊性，我们可以在遍历数组的时候保存两个值：
一个candidate，用来保存数组中遍历到的某个数字；一个nTimes，表示当前数字的出现次数，其中，`nTimes初始化为1，candidate初始化为numbers[0]`
当我们遍历到数组中下一个数字的时候：
1. 判断票数
1.1 如果`nTimes ==0`
用candidate保存下一个数字，并把nTimes重新设为1
1.2. 如果`nTimes ！=0`，对比下一个数字与候选人
1.2.1 如果下一个数字与之前candidate保存的数字相同，则nTimes加1
1.2.2 如果下一个数字与之前candidate保存的数字不同，则nTimes减1

直到遍历完数组中的所有数字为止。

举个例子，假定数组为{0, 1, 2, 1, 1}，按照上述思路执行的步骤如下：
1. 开始时，candidate保存数字0，nTimes初始化为1；
2. 然后遍历到数字1，与数字0不同，则nTimes减1变为0；
3. 因为nTimes变为了0，故candidate保存下一个遍历到的数字2，且nTimes被重新设为1；
4. 继续遍历到第4个数字1，与之前candidate保存的数字2不同，故nTimes减1变为0；
5. 因nTimes再次被变为了0，故我们让candidate保存下一个遍历到的数字1，且nTimes被重新设为1。最后返回的就是最后一次把nTimes设为1的数字1。

# 代码实现
本题采用第五种实现方式

```java
public class _39{
    public static int moreThanHalfNum(int[] numbers) {
        if (numbers == null || numbers.length < 1) { // 输入校验
            throw new IllegalArgumentException("array length must large than 0");
        }

        int candidate= numbers[0];  // 记录众数(候选人)
        int nTimes = 1;  // 与当前记录的数 不同的数 的个数（票数）
        for (int i = 1; i < numbers.length; i++) { // 从第二个数开始向后找
            if (nTimes == 0) {//1.1------------------------------------
                candidate= numbers[i];
                nTimes = 1;
            }else if (candidate== numbers[i]) {//1.2.1--------------------------
                nTimes ++;
            }else {//1.2.1------------------------------------
                nTimes --;
            }
        }
       
        nTimes = 0;// 统计candidate的出现次数（判断输入是否合题意）
        for (int number : numbers) {
            if (candidate== number) {
                nTimes ++;
            }
        }
        if (nTimes > numbers.length / 2) {   // 如果出现次数大于数组的一半就返回对应的值
            return candidate;
        }else {
            throw new IllegalArgumentException("invalid input");
        }
    }

    public static void main(String[] args) {
        int numbers[] = {1, 2, 3, 2, 2, 2, 5, 4, 2};
        System.out.println(moreThanHalfNum(numbers));
    }
}
```

![数组中出现次数超过一半的数字](http://p7vxw6hv7.bkt.clouddn.com/18-7-19/3525638.jpg)

参考文章
[出现次数超过一半的数字](https://github.com/julycoding/The-Art-Of-Programming-By-July/blob/master/ebook/zh/04.03.md)