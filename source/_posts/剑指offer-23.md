---
title: 剑指offer_23
date: 2018-05-18 21:36:59
tags: 剑指offer
categories: 简单算法
---
# 题目：链表中环的入口节点
如果一个链表中包含环，请找出该链表的环的入口结点。
<!-- more -->
# 举例说明
如：在1->2->3->4->5->6->3的链表中，包含一个环，环的入口节点是3。
# 思路
## 哈希表
遍历整个链表，并将链表结点存入哈希表中，如果遍历到某个链表结点已存在，那么该点即为环的入口结点；
## 断开链表法
前后两个指针，破坏他们之间的链接，修改链表。
但是这个方法会破坏链表结构。

### 代码

```java
public class _23 {
	private static class ListNode {
		private int val;
		private ListNode next;

		public ListNode() {
		};

		public ListNode(int value) {
			this.val = value;
		}
	}

	public static ListNode meetingNode(ListNode head) {
		if (head == null) {
			return null;
		}
		ListNode slow = head;
		ListNode fast = head.next;
		if (fast == null) {
			return null;
		}//断开遍历过的链表
		while (fast != null) {
			slow.next = null;
			slow = fast;
			fast = fast.next;
		}
		return slow;
	}

	public static void main(String[] args) {
		ListNode n1 = new ListNode(1);
		ListNode n2 = new ListNode(2);
		ListNode n3 = new ListNode(3);
		ListNode n4 = new ListNode(4);
		ListNode n5 = new ListNode(5);
		ListNode n6 = new ListNode(6);
		n1.next = n2;
		n2.next = n3;
		n3.next = n4;
		n4.next = n5;
		n5.next = n6;
		n6.next = n3;
		System.out.println(meetingNode(n1).val);
	}
}
```

### 输出
![image](http://upload-images.jianshu.io/upload_images/11861611-e1a8574d4b367427.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 快慢指针法
1. 确定链表中是否包含环：双指针，一个每次移动一步，一个每次移动两步，如果两个指针最后相遇，那么就包**含环**。（注意，移动两步的指针要判断判断其第一步不为空，才能移动第二步）
2. 确定环中点节点数目：在上面相遇的节点的基础上，移动一个指针，并计数，当指针回到该节点时，确定**环中节点数目**。
3. 找到环的入口节点：从头开始，使用两个指针，第一个指针先移动n步（其中n为确定的环中的节点数目），第二个指针再开始**同时同速移动**，两个指针相遇的节点即为入口节点。
## 不用计算环节点数的快慢指针法
如果链表存在环，我们无需计算环的长度n，只需在相遇时，让一个指针在相遇点出发，另一个指针在链表首部出发，然后两个同时同速移动，它们相遇点就是环的入口处。
### 参考文章
[牛客网讨论区](https://www.nowcoder.com/questionTerminal/253d2c59ec3e4bc68da16833f79a38e4)
有部分整理和修改
### 推导
![image](http://upload-images.jianshu.io/upload_images/11861611-4a65b170f42cb9e8.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 条件
假设x为环前面的路程（黑色路程），a为环入口到相遇点的路程（蓝色路程，假设顺时针走）， c为环的长度（蓝色+橙色路程）
- 相遇的时候
  - 从环的角度--路程差
慢指针走的路程为  Sslow = x + m * c + a
快指针走的路程为 Sfast = x + n * c + a
  - 从速度（2 *）与时间（一样）角度--路程差
2 Sslow = Sfast
  - 联立方程
2 * ( x + m*c + a ) = (x + n *c + a)
从而可以推导出：
x = (n - 2 * m )*c - a
= (n - 2 *m -1 )*c + c - a
- 即环前面的路程 = 数个环的长度（可能为0） + c - a
什么是c - a？这是相遇点后，环后面部分的路程。（橙色路程）
- 所以，我们可以让一个指针从起点A开始走，让一个指针从相遇点B开始继续往后走，
2个指针速度一样，那么，当从原点的指针走到环入口点的时候（此时刚好走了x）
从相遇点开始走的那个指针也一定刚好到达环入口点。
所以2者会相遇，且恰好相遇在环的入口点。
- 最后，判断是否有环，且找环的算法复杂度为：
时间复杂度：O(n)
空间复杂度：O(1)
### 代码

```java
public class _23 {
	private static class ListNode {
		private int val;
		private ListNode next;

		public ListNode() {
		};

		public ListNode(int value) {
			this.val = value;
		}
	}
	
    public static ListNode meetingNode(ListNode head) {
        ListNode fast = head;
        ListNode slow = head;
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
            if (fast == slow) {
                break;
            }
        }
        // 链表中没有环
        if (fast == null || fast.next == null) {
            return null;
        }
        // fast重新指向第一个结点
        fast = head;
        while (fast != slow) {
            fast = fast.next;
            slow = slow.next;
        }
        return fast;
    }

	public static void main(String[] args) {
		ListNode n1 = new ListNode(1);
		ListNode n2 = new ListNode(2);
		ListNode n3 = new ListNode(3);
		ListNode n4 = new ListNode(4);
		ListNode n5 = new ListNode(5);
		ListNode n6 = new ListNode(6);
		n1.next = n2;
		n2.next = n3;
		n3.next = n4;
		n4.next = n5;
		n5.next = n6;
		n6.next = n3;
		System.out.println(meetingNode(n1).val);
	}
}
```

### 输出
![image](http://upload-images.jianshu.io/upload_images/11861611-253868c0bb539f18.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)