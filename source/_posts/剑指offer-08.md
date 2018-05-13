---
title: 剑指offer_08
date: 2018-05-12 14:06:03
tags: 剑指offer
categories: 简单算法
---
# 题目：二叉树中序遍历顺序的下一个结点
给定一棵二叉树和其中的一个结点，如何找出中序遍历顺序的下一个结点？树中的结点除了有两个分别指向左右子结点的指针以外，还有一个指向父节点的指针。
<!-- more -->
# 举例说明

```java
// 测试用例使用的树
    	//       1
    	//    2      3
    	//  4   5   6  7   order:4 2 5 1 6 3 7
//输入此树和节点5，则应该返回节点1
```

# 解题思路
- 如果节点存在右子树，直接往下找：
那么它的下一个节点就是**右子树中的最左节点**；
- 如果节点没有右子树，则看该节点与父节点关系： 
  - 节点为其父节点的左子节点，那么其**父节点**就是它的下一个节点；
  - 节点为其父节点的右子节点，那么需要沿其父指针一直向上遍历，一直找到某个节点是其父节点的左子节点为止，那么这个节点的**父节点**即是需要寻找的下一个节点。
## 代码

```java
public class _08 {
    private static class BinaryTreeNode {
        private int val;
        private BinaryTreeNode left;
        private BinaryTreeNode right;
        private BinaryTreeNode parent;
        public BinaryTreeNode() {
        }
        public BinaryTreeNode(int val) {
            this.val = val;
        }
        @Override
        public String toString() {
            return val + "";
        }
    }
    public static BinaryTreeNode getNext(BinaryTreeNode node) {
        if (node == null) {
            return null;
        }
        BinaryTreeNode target = null; // 保存要查找的下一个节点
        if(node.right!=null) {
        	target=node.right;//右子树
        	while(target.left!=null) {
        		target=target.left;//最左节点
        	}
        	return target;
        }else if(node.parent!=null) {
        	target=node.parent;
        	BinaryTreeNode cur=node;
               //一直找到某个节点是其父节点的左子节点为止
        	while(target!=null&&cur!=target.left) {
        		cur=target;
        		target=target.parent;
        	}
        	return target;
        }
        return null;
     }
    private static void assemble(BinaryTreeNode node,BinaryTreeNode left,BinaryTreeNode right,BinaryTreeNode parent) {
    	node.left=left;
    	node.right=right;
    	node.parent=parent;
    }
    public static void main(String[] args) {
    	//       1
    	//    2      3
    	//  4   5   6  7   order:4 2 5 1 6 3 7
		BinaryTreeNode n1=new BinaryTreeNode(1);//6
		BinaryTreeNode n2=new BinaryTreeNode(2);//5
		BinaryTreeNode n3=new BinaryTreeNode(3);//7
		BinaryTreeNode n4=new BinaryTreeNode(4);//2
		BinaryTreeNode n5=new BinaryTreeNode(5);//1
		BinaryTreeNode n6=new BinaryTreeNode(6);//3
		BinaryTreeNode n7=new BinaryTreeNode(7);//null
		assemble(n1,n2,n3,null);
		assemble(n2,n4,n5,n1);
		assemble(n3,n6,n7,n1);
		assemble(n4,null,null,n2);
		assemble(n5,null,null,n2);
		assemble(n6,null,null,n3);
		assemble(n7,null,null,n3);
		System.out.println(_08.getNext(n1));
		System.out.println(_08.getNext(n2));
		System.out.println(_08.getNext(n3));
		System.out.println(_08.getNext(n4));
		System.out.println(_08.getNext(n5));
		System.out.println(_08.getNext(n6));
		System.out.println(_08.getNext(n7));
	}
}
```
## 输出
![测试用例输出](http://upload-images.jianshu.io/upload_images/11861611-ed180800efe05185.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)