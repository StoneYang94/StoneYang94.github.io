---
title: 剑指offer-34
date: 2018-07-19 21:44:44
tags: 剑指offer
categories: 简单算法
---
# 题目：二叉树中和为某一值的路径
输入一棵二叉树和一个整数， 打印出二叉树中`结点值的和  =  输入整数`的所有路径。
路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。

<!-- more -->

## 二叉树节点

```java
    public static class BinaryTreeNode {
        int value;
        BinaryTreeNode left;
        BinaryTreeNode right;
        public BinaryTreeNode(){
        }
        public BinaryTreeNode(int value){
            this.value = value;
        }
    }
```

## 举例说明

二叉树：

```
         10
       /  \
      5    12
     / \   
    4  7 
```

## 思路
由于路径是从根结点出发到叶结点， **也就是说路径总是以根结点为起始点**，因此我们首先需要遍历根结点。`在树的前序、中序、后序三种遍历方式中，只有前序遍历是首先访问根结点的`。

1. 当用前序遍历的方式访问到某一结点时，我们把该结点`添加`到路径上，并`累加`该结点的值
2. 比较当前记录的和与期望的和
2.1 如果`当前记录的和 < 期望的和` 递归处理左、右子树
2.2 如果`当前记录的和 = 期望的和`且`该结点为叶结点`，则当前的路径符合要求，把路径打印出来
如果当前结点不是叶结点，则继续访问它的子结点。
3. 当前`结点访问结束后`，递归函数将自动回到它的父结点。这里要注意的是：在函数退出之前要在路径上删除当前结点并`减去当前结点的值`，以确保返回父结点时路径刚好是从根结点到父结点的路径。

不难看出保存路径的数据结构实际上是一个栈， 因为路径要与递归调用状态一致， 而递归调用的本质就是一个压栈和出栈的过程。

# 代码实现

```java
import java.util.ArrayList;
import java.util.List;

public class _34{
    public static class BinaryTreeNode {
        int value;
        BinaryTreeNode left;
        BinaryTreeNode right;
        public BinaryTreeNode(){
        }
        public BinaryTreeNode(int value){
            this.value = value;
        }
    }

    public static void findPath(BinaryTreeNode node, int expectedSum) {
        List<Integer> list = new ArrayList<>(); // 存放根结点到当前处理结点的所经过的结点
        if (node!= null) {
        	findPath(node, 0, expectedSum, list);
        }
    }

    /**
     * @param root        当前要处理的结点
     * @param curSum      当前记录的和（还未加上当前结点的值）
     * @param expectedSum 要求的路径和
     * @param result      根结点到当前处理结点的所经过的结点，（还未包括当前结点）
     */
    public static void findPath(BinaryTreeNode node, int curSum, int expectedSum, List<Integer> result) {
        if (node!= null) {//1.-------------------------------------------------
            curSum += node.value;    // 加上当前结点的值  
            result.add(node.value);  // 将当前结点入队
            if (curSum < expectedSum) { //2.1----------------------------------   
                findPath(node.left, curSum, expectedSum, result);// 递归处理左子树
                findPath(node.right, curSum, expectedSum, result);// 递归处理右子树
            }else if (curSum == expectedSum) {  //2.2-----------------------  
                if (node.left == null && node.right == null) {
                    System.out.println(result);
                }
            }//3.-------------------------------------------------
            result.remove(result.size() - 1);   // 移除当前结点
        }
    }

    public static void main(String[] args) {
        //            10
        //         /      \
        //        5        12
        //       /\
        //      4  7
        BinaryTreeNode n1 = new BinaryTreeNode(10);
        BinaryTreeNode n2 = new BinaryTreeNode(5);
        BinaryTreeNode n3 = new BinaryTreeNode(12);
        BinaryTreeNode n4 = new BinaryTreeNode(4);
        BinaryTreeNode n5 = new BinaryTreeNode(7);
        n1.left = n2;
        n1.right = n3;
        n2.left = n4;
        n2.right = n5;

        System.out.println("findPath(node, 22)--------------------------");
        findPath(n1, 22);
        System.out.println("findPath(node, 19)--------------------------");
        findPath(n1, 19);
        System.out.println("findPath(node, 15)--------------------------");
        findPath(n1, 15);
    }
}
```

![二叉树中和为某一值的路径](http://p7vxw6hv7.bkt.clouddn.com/18-7-19/83988597.jpg)