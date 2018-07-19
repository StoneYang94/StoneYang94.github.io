---
title: '剑指offer-27,28'
date: 2018-07-17 22:28:42
tags: 剑指offer
categories: 简单算法
---
题目：
1. 二叉树的镜像
2. 对称的二叉树

<!-- more -->

# 题目：二叉树的镜像
请完成一个函数，输入一个二叉树，该函数输出它的镜像。

## 二叉树节点

```java
public static class BinaryTreeNode {
    int value;
    BinaryTreeNode left;
    BinaryTreeNode right;
}
```

## 举例说明

原二叉树：（中序遍历：5 6 7 8 9 10 11）

```
         8
       /  \
      6    10
     / \   / \
    5  7   9 11
```

镜像二叉树：（中序遍历：11 10 9 8 7 6 5）

```
         8
       /   \
      10    6
     / \   / \
    11 9  7  5
```

## 思路
二叉树的镜像相当于是将每个含有子结点的结点的左右子结点进行交换。利用递归实现。

![左右子结点进行交换](http://p7vxw6hv7.bkt.clouddn.com/18-7-17/71628782.jpg)

## 代码实现

```java
public class _27{
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

    public static void mirrorRecursively(BinaryTreeNode node){
        if(node == null) {
            return;	
        }
        if(node.left == null && node.right == null) {
            return;	
        }
        BinaryTreeNode temp = node.left;
        node.left= node.right;
        node.right = temp;
        if(node.left != null)
        	mirrorRecursively(node.left);
        if(node.right != null)
        	mirrorRecursively(node.right);
    }


    public static void printTree(BinaryTreeNode node) {
        if (node != null) {
            printTree(node.left);
            System.out.print(node.value + " ");
            printTree(node.right);
        }
    }

    public static void main(String[] args) {
        //       8
        //    /    \
        //   6     10
        //  / \   / \
        // 5   7 9  11
        BinaryTreeNode n1 = new BinaryTreeNode(8);
        BinaryTreeNode n2 = new BinaryTreeNode(6);
        BinaryTreeNode n3 = new BinaryTreeNode(10);
        BinaryTreeNode n4 = new BinaryTreeNode(5);
        BinaryTreeNode n5 = new BinaryTreeNode(7);
        BinaryTreeNode n6 = new BinaryTreeNode(9);
        BinaryTreeNode n7 = new BinaryTreeNode(11);
        n1.left = n2;
        n1.right = n3;
        n2.left = n4;
        n2.right = n5;
        n3.left = n6;
        n3.right = n7;
        printTree(n1);
        System.out.println();
        mirrorRecursively(n1);
        printTree(n1);
    }
}
```

![二叉树的镜像](http://p7vxw6hv7.bkt.clouddn.com/18-7-17/78813554.jpg)

# 题目：对称的二叉树
请实现一个函数，用来判断一棵二叉树是不是对称的。如果二叉树和它的镜像一样，那么它是对称的。

## 举例说明

```
         8
       /  \
      6    10
     / \   / \
    5  7   9 11 不是对称的二叉树
```

```
         8
       /  \
      6    6
     / \   / \
    5  7   7 5  是对称的二叉树
```

## 思路
如果一颗二叉树是对称的，需要满足：
1. 当前节点的值相等
2. 节点的左子树对称，节点的右子树对称
注意这里的对称，是说镜像相同。而镜像与原来的是反的。即左子树的左子树与右子树的右子树相同。

## 代码实现

```java
public class _28{
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
	
	public static boolean isSymmetrical(BinaryTreeNode Root) {
		if (Root == null)
			return true;
		return isSymmetrical(Root.left, Root.right);
	}
	
	private static boolean isSymmetrical(BinaryTreeNode left, BinaryTreeNode right) {
		if (left == null && right == null)//都是空
			return true;
		if (left == null || right == null)//只有一个空
			return false;
		if (left.value != right.value)
			return false;
		return isSymmetrical(left.left, right.right) && isSymmetrical(left.right, right.left);
	}
	
	public static void main(String[] args) {
        BinaryTreeNode n1 = new BinaryTreeNode(8);
        BinaryTreeNode n2 = new BinaryTreeNode(6);
        BinaryTreeNode n3 = new BinaryTreeNode(10);
        BinaryTreeNode n4 = new BinaryTreeNode(5);
        BinaryTreeNode n5 = new BinaryTreeNode(7);
        BinaryTreeNode n6 = new BinaryTreeNode(9);
        BinaryTreeNode n7 = new BinaryTreeNode(11);
        n1.left = n2;
        n1.right = n3;
        n2.left = n4;
        n2.right = n5;
        n3.left = n6;
        n3.right = n7;
        System.out.println(isSymmetrical(n1));
        BinaryTreeNode n8 = new BinaryTreeNode(8);
        BinaryTreeNode n9 = new BinaryTreeNode(6);
        BinaryTreeNode n10 = new BinaryTreeNode(6);
        BinaryTreeNode n11 = new BinaryTreeNode(5);
        BinaryTreeNode n12 = new BinaryTreeNode(7);
        BinaryTreeNode n13 = new BinaryTreeNode(7);
        BinaryTreeNode n14 = new BinaryTreeNode(5);
        n8.left = n9;
        n8.right = n10;
        n9.left = n11;
        n9.right = n12;
        n10.left = n13;
        n10.right = n14;
        System.out.println(isSymmetrical(n8));
	}
}
```

![对称的二叉树](http://p7vxw6hv7.bkt.clouddn.com/18-7-17/61189498.jpg)




