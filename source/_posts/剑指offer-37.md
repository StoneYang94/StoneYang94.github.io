---
title: 剑指offer-37
date: 2018-07-20 18:55:32
tags: 剑指offer
categories: 简单算法
---
# 题目：序列化二叉树
请实现两个函数，分别用来序列化和反序列化二叉树。
将树写入一个文件被称为“序列化”，读取文件后重建同样的二叉树被称为“反序列化”。

<!-- more -->

## 二叉树节点

```java
private static class BinaryTreeNode {
    private int val;
    private BinaryTreeNode left;
    private BinaryTreeNode right;

    public BinaryTreeNode() {
    }
    public BinaryTreeNode(int val) {
        this.val = val;
    }
}
```

## 举例说明

二叉树

```
       1
   /     \
  2       3
 /       / \
 4       5  6
```

按前序遍历（只有前序遍历从根节点开始）系列化之后：

```
 1,2,4,$,$,$,3,5,$,$,6,$,$,
```

## 思路
- 以前的先序和后序遍历序列重构二叉树存在缺点： 
1. 要求二叉树中没有数值相同的结点
2. 要求整个序列都读出后才能开始重构，如果序列是流式输入，那么就需要等待序列输出完毕才能完成二叉树的重构
- 利用先序遍历
1. node之间用`,`隔开
2. 保存二叉树空结点为`$`
利用这样的序列，就可以规避上面存在的两种缺点。

# 代码实现

```java
import java.util.LinkedList;
import java.util.Queue;

public class _37{
    private static class BinaryTreeNode {
        private int value;
        private BinaryTreeNode left;
        private BinaryTreeNode right;

        public BinaryTreeNode() {
        }
        public BinaryTreeNode(int value) {
            this.value = value;
        }
    }


	public static String serialize(BinaryTreeNode root) {
		if (root == null) {
			return "$,";
		}
		String res = root.value + ",";
		res += serialize(root.left);
		res += serialize(root.right);
		return res;
	}

	public static BinaryTreeNode deserialize(String preStr) {
		String[] values = preStr.split(",");//按之前的分隔符，得到一个数组
		Queue<String> queue = new LinkedList<String>();
		for (int i = 0; i < values.length; i++) {
			queue.offer(values[i]);
		}
		return deserialize(queue);
	}
	
	public static BinaryTreeNode deserialize(Queue<String> queue) {
		String value = queue.poll();//移除并返问队列头部的元素(如果队列为空，返回null)
		if (value.equals("$")) {
			return null;
		}//分别递归消费这个队列
		BinaryTreeNode root = new BinaryTreeNode(Integer.valueOf(value));
		root.left = deserialize(queue);
		root.right = deserialize(queue);
		return root;
	}

    public static void main(String[] args) {
        //       1
        //    /    \
        //   2     3
        //  /    / \
        // 4    5  6
        BinaryTreeNode n1 = new BinaryTreeNode(1);
        BinaryTreeNode n2 = new BinaryTreeNode(2);
        BinaryTreeNode n3 = new BinaryTreeNode(3);
        BinaryTreeNode n4 = new BinaryTreeNode(4);
        BinaryTreeNode n5 = new BinaryTreeNode(5);
        BinaryTreeNode n6 = new BinaryTreeNode(6);
        n1.left = n2;
        n1.right = n3;
        n2.left = n4;
        n3.left = n5;
        n3.right = n6;
        
        String result = serialize(n1);
        System.out.println("serialize: "+result);
        BinaryTreeNode root = deserialize(result) ;
        System.out.println("deserialize: ");
        printTree(root);
    }

    public static void printTree(BinaryTreeNode node) {
        if (node != null) {
            System.out.print(node.value + " ");
            printTree(node.left);
            printTree(node.right);
        }
    }
}
```

![序列化二叉树](http://p7vxw6hv7.bkt.clouddn.com/18-7-19/22915139.jpg)