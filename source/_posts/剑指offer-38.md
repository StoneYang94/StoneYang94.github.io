---
title: 剑指offer-38
date: 2018-07-22 18:58:28
tags: 剑指offer
categories: 简单算法
---
# 题目：字符串的排列
输入一个字符串，打印出该字符串中字符的所有排列。

<!-- more -->

## 举例说明
例如输入字符串abc。则打印出由字符a、b、c 所能排列出来的所有字符串abc、acb、bac 、bca、cab 和cba 。

## 思路

![基于回溯法思想](http://p7vxw6hv7.bkt.clouddn.com/18-7-22/47513607.jpg)

把需要全排列的字符串分为两部分看待：
字符串的第一个字符和第一个字符后面的所有字符。
首先固定第一个字符，求后面所有字符的排列。这个时候我们仍把后面的所有字符分成两部分：后面字符的第一个字符，以及这个字符之后的所有字符。然后把第一个字符逐一和它后面的字符交换。

步骤：
1. 交换字符
将字符串分为两部分，第一部分是第一个字符，另外一部分是后面的字符串。每次循环将`第一个字符`与`后面的每个字符`进行交换
2. 求后面所有字符的排列
对后一部分字符串，视为当前字符串，进行前面同样的操作
//3. 子字符串操作完成后，需要恢复第一个位置的原本字符

# 代码实现

```java
public class _38{
    public static void permutation(char[] chars) {
        if (chars == null || chars.length < 1) {// 输入较验
            return;
        }
        permutation(chars, 0);
    }

    public static void permutation(char[] chars, int begin) {
        if (chars.length - 1 == begin) { // 如果是最后一个元素了，就输出排列结果
            System.out.print(new String(chars) + " ");
        } else {
            char tmp;
            // 对当前还未处理的字符串进行处理，每个字符都可以作为当前处理位置的元素
            for (int i = begin; i < chars.length; i++) {//1---------------
                tmp = chars[begin];
                chars[begin] = chars[i];
                chars[i] = tmp;
            //2---------------
                permutation(chars, begin + 1); // 处理下一个位置
            }
        }
    }

    public static void main(String[] args) {
        char[] c1 = {'a', 'b', 'c'};
        permutation(c1);
        System.out.println();

        char[] c2 = {'a', 'b', 'c', 'd'};
        permutation(c2);
    }
}
```

![字符串的排列](http://p7vxw6hv7.bkt.clouddn.com/18-7-19/56419957.jpg)