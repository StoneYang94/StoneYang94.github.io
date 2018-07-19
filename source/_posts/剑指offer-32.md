---
title: 剑指offer-32
date: 2018-07-19 21:08:18
tags: 剑指offer
categories: 简单算法
---
题目：从上到下打印二叉树
1. 不分行从上到下打印二叉树
2. 分行从上到下打印二叉树
3. 之字形打印二叉树

<!-- more -->

# 题目 1. 不分行从上到下打印二叉树
从上到下打印出二叉树的每个节点，同一层的节点按照从左到右的顺序打印。

## 二叉树结点

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
如下的二叉树

```
            8
         /     \
        6       10
       /  \      / \
      5   7     9  11
```

则依次打印出`8  6 10 5 7 9 11`

## 解题思路
考查树的遍历算法。
从上到下打印二叉树的规律：
1. 每一次打印一个结点的时候，如果该结点有子结点， 则把该结点的`子结点放到一个队列的末尾`
2. 接下来到队列的`头部取出`最早进入队列的结点
3. 重复前面的打印操作，直至队列中`所有的结点都被打印出来为止`

## 代码实现：

```java
import java.util.Queue;
import java.util.LinkedList;

public class _3200{
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

    public static void printFromTopToBottom(BinaryTreeNode root) {
        if (root != null) { // 当结点非空时才进行操作
            // 用于存放还未遍历的元素
            Queue<BinaryTreeNode> list = new LinkedList<>();
            list.add(root);            //   根结点入队
            BinaryTreeNode cur;  //   当前处理的结点
            while (!list.isEmpty() ) { 
                cur = list.remove(); // 删除队首
                System.out.print(cur.value + " ");  //  输出队首元素的值
                // 如果左子结点不为空，则左子结点入队
                if (cur.left != null) {
                    list.add(cur.left);
                }
                // 如果右子结点不为空，则左子结点入队
                if (cur.right != null) {
                    list.add(cur.right);
                }
            }
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
        printFromTopToBottom(n1);
    }
}
```

![不分行从上到下打印二叉树](http://p7vxw6hv7.bkt.clouddn.com/18-7-19/78671487.jpg)

扩展：
- 从上到下按层遍历二叉树，从本质上来说就是`广度优先遍历二叉树`
- 不管是广度优先遍历一幅有向图还是一棵树，都要用到`队列`
- 首先把`起始节点（树中为根结点）`放入队列，接下来每次从队列的头部取出一个节点，遍历这个节点后把它`能到达的节点（树中为子结点）`都依次放入队列。重复这个遍历过程，直到队列中的节点全部都遍历为止

# 题目 2. 分行从上到下打印二叉树
从上到下按层打印二叉树，同一层的节点按从左到右的顺序打印，每一层打印到一行。

## 举例说明
如下的二叉树

```
            8
         /     \
        6       10
       /  \      / \
      5   7     9  11
```

则依次打印出
```
8  
6 10 
5 7 9 11
```

## 解题思路
用`队列`来保存要打印的节点。 
同时我们需要两个变量：一个变量表示在`当前层中还没有打印的节点数`；另一个变量表示`下一层节点的数目`

## 代码实现

```java
import java.util.LinkedList;
import java.util.List;

public class _3201{
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

    public static void print(BinaryTreeNode root) {
        if (root == null) {
            return;
        }
        List<BinaryTreeNode> list = new LinkedList<>();
        BinaryTreeNode node;
        int current = 1;// 当前层的结点个数,开始时只有根节点一个
        int next = 0;// 下一层的结点个数
        list.add(root);
        while (list.size() > 0) {
            node = list.remove(0);
            current--;
            System.out.print(" "+node.value);
            if (node.left != null) {
                list.add(node.left);
                next++;
            }
            if (node.right != null) {
                list.add(node.right);
                next++;
            }
            if (current ==0) {
                System.out.println();//本层打印完了
                current = next;//现在处理下一层
                next = 0;
            }
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
        print(n1);
    }
}
```

![分行从上到下打印二叉树](http://p7vxw6hv7.bkt.clouddn.com/18-7-19/20633190.jpg)

# 题目 3. 之字形打印二叉树
请实现一个函数按照之字形打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右至左的顺序打印，第三行按照从左到右的顺序打印，其他行以此类推。（每一层打印到一行。）

## 举例说明
如下的二叉树

```
            8
         /     \
        6       10
       /  \      / \
      5   7     9  11
```

则依次打印出
```
8  
10 6 
5 7 9 11
```

## 解题思路
按之字形顺序打印二叉树需要两个栈。
- 我们在打印某一行结点时，把下一层的子结点保存到相应的栈里
- 如果当前打印的是奇数层，则先保存左子结点再保存右子结点到一个栈里
- 如果当前打印的是偶数层，则先保存右子结点再保存左子结点到第二个栈里

## 代码实现

```java
import java.util.LinkedList;
import java.util.List;

public class _3202{
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

    public static void print(BinaryTreeNode root) {
        if (root == null) {
            return;
        }
        List<BinaryTreeNode> current = new LinkedList<>();
        List<BinaryTreeNode> reverse = new LinkedList<>();
        int flag = 0;//bool flag = false;
        BinaryTreeNode node;
        current.add(root);
        while (current.size() > 0) {
            // 从最后一个开始取，相当于栈
            node = current.remove(current.size() - 1);
            System.out.print(" "+node.val);
            if (flag == 0) {// if (!flag )   // 当前是从左往右打印的，那就按从左往右入栈
                if (node.left != null) {
                    reverse.add(node.left);
                }
                if (node.right != null) {
                    reverse.add(node.right);
                }
            }else { // 当前是从右往左打印的，那就按从右往左入栈
                if (node.right != null) {
                    reverse.add(node.right);
                }
                if (node.left != null) {
                    reverse.add(node.left);
                }
            }
            if (current.size() == 0) {//当前层处理完了
                flag = 1 - flag;//flag != ;
                List<BinaryTreeNode> tmp = current;
                current = reverse;
                reverse = tmp;
                System.out.println();
            }
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
        print(n1);
    }
}
```

![之字形打印二叉树](http://p7vxw6hv7.bkt.clouddn.com/18-7-19/64149054.jpg)







