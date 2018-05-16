---
title: 剑指offer_18
date: 2018-05-15 19:53:20
tags: 剑指offer
categories: 简单算法
---
题目：
-  在O(1)的时间内删除链表节点
-  删除链表中重复的节点
<!-- more -->
# 题目1：在O(1)的时间内删除链表节点
给定单向链表的头指针和一个节点指针，定义一个函数在O(1)时间内删除该节点。
# 举例说明
链表1->2->3，输入头结点head和待删除结点n2，输出删除之后的头结点，此时链表1->2。
# 思路
## 常规思路
- 一般单向列表删除结点，就是从头遍历，找到这个结点之前的结点，指向这个结点之后的结点，就算是删除了这个结点。
- 这种方法的时间复杂度为O(n) 。
## 复制结点法
- 题目要求在O(1)时间内完成，所以应该避免遍历。 
- 我们遍历的目的是为了找到这个结点前面的那个结点，所以我们可以 将待删除结点的**下一个结点的值赋给**待删除的结点，然后删除一个结点，就达到了删除结点的目的。
- 对于n-1个非尾节点，O(1)时间内可以删除，如果是尾节点则O(n)，平均时间复杂度为[O(1)∗(n−1)+O(n)∗1]/n，时间复杂度还是O(1)。符合要求。不过该方法并没有判断需要删除的节点是否在链表中，为达到时间复杂度为O(1)的要求，将判断节点是否存在的责任推给了该方法的调用者。
### 注意边界条件的特殊处理
1)正常, 多个结点，删除的不是尾结点。 
2)只有一个结点，删除尾结点（也是头结点） 
3)有多个结点，删除尾结点
### 代码

```java
public class _18_1 {
	private static class ListNode {
		int val;
		ListNode next;

		ListNode() {
		};

		ListNode(int value) {
			this.val = value;
		}
	}

	// 这个方法和文本上的不一样，书上的没有返回值，这个因为JAVA值传递的原因， 如果删除的结点是头结点，如果不采用返回值的方式，那么头结点永远删除不了
	private static ListNode deleteNode(ListNode head, ListNode nodeToBeDeleted) {
		if (head == null || nodeToBeDeleted == null)
			return head;
		// 要删除的节点不是尾节点 O(1)------------
		if (nodeToBeDeleted.next != null) {
			nodeToBeDeleted.val = nodeToBeDeleted.next.val;
			// 待删除的结点的下一个指向原先待删除引号的下下个结点，即将待删除的下一个结点删除
			nodeToBeDeleted.next = nodeToBeDeleted.next.next;
		} else if (head == nodeToBeDeleted) { // 要删除头节点，而且链表只有一个节点 O(1)--------------
			return head.next;
		} else {
			ListNode tmp = head; // 要删除尾节点，而且链表有多个节点 O(n)----------------
			while (tmp.next != nodeToBeDeleted) {
				tmp = tmp.next;
			}
			tmp.next = null;
			nodeToBeDeleted = null;
		}
		return head;
	}

	private static void printList(ListNode head) {
		while (head != null) {
			System.out.print(head.val + "-> ");
			head = head.next;
		}
		System.out.println("null");
	}

	public static void main(String[] args) {
		// 多个节点，并且删除的不是尾结点
		ListNode n1 = new ListNode(1);
		ListNode n2 = new ListNode(2);
		ListNode n3 = new ListNode(3);
		n1.next = n2;
		n2.next = n3;
		System.out.println("多个节点，并且删除的不是尾结点--------");
		System.out.println("调用前");
		printList(n1);
		System.out.println("调用后");
		printList(deleteNode(n1, n2));
		// 只有一个节点
		System.out.println("只有一个节点----------");
		ListNode n4 = new ListNode(4);
		System.out.println("调用前");
		printList(n4);
		System.out.println("调用后");
		printList(deleteNode(n4, n4));
		// 多个节点，删除尾结点
		ListNode n5 = new ListNode(5);
		ListNode n6 = new ListNode(6);
		ListNode n7 = new ListNode(7);
		n5.next = n6;
		n6.next = n7;
		System.out.println("多个节点，删除尾结点------------");
		System.out.println("调用前");
		printList(n5);
		System.out.println("调用后");
		printList(deleteNode(n5, n7));
	}
}
```

### 输出
![image](http://upload-images.jianshu.io/upload_images/11861611-ec1485e06e13784f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


# 题目2：删除链表中重复的节点
在一个排序的链表中，存在重复的结点，请删除该链表中重复的结点，重复的结点不保留，返回链表头指针。
# 举例说明
链表1->2->3->3->3->4->4>5，输入头结点head，输出删除之后的头结点，此时链表1->2>5。
# 思路
- 我们从头遍历整个链表。如果当前结点的值与下一个结点的值相同，那么它们就是重复的结点，都可以被删除。
- 为了保证删除之后的链表仍然是相连的而没有中间断开，我们要把**当前的前一个结点和后面值比当前结点的值要大的结点相连**。我们要确保prev要始终与下一个没有重复的结点连接在一起。
- 头结点可能与后面的结点重复，也就是说**头结点也可能被删除**，所以在链表头添加一个结点。 
## 代码

```java
public class _18_2 {
	private static class ListNode {
		int val;
		ListNode next;

		ListNode() {
		}

		ListNode(int value) {
			this.val = value;
		}

	}

	private static ListNode deleteDuplication(ListNode head) {
		// 为null
		if (head == null) {
			return null;
		}

		// 只有一个结点
		if (head.next == null) {
			return head;
		}

		// 临时的头结点
		ListNode root = new ListNode();
		root.next = head;
		// 记录前驱结点
		ListNode prev = root;
		// 记录当前处理的结点
		ListNode node = head;
		while (node != null && node.next != null) {
			// 有重复结点，与node值相同的结点都要删除
			if (node.val == node.next.val) {
				// 找到下一个不同值的节点，注意其有可能也是重复节点
				while (node.next != null && node.next.val == node.val) {
					node = node.next;
				}
				// 指向下一个节点，prev.next也可能是重复结点
				// 所以prev不要移动到下一个结点
				prev.next = node.next;
			}
			// 相邻两个值不同，说明node不可删除，要保留
			else {
				prev.next = node;
				prev = prev.next;

			}
			node = node.next;
		}

		return root.next;

	}

	private static void printList(ListNode head) {
		while (head != null) {
			System.out.print(head.val + "->");
			head = head.next;
		}
		System.out.println("null");
	}

	public static void main(String[] args) {
		ListNode n1 = new ListNode(1);
		ListNode n2 = new ListNode(2);
		ListNode n3 = new ListNode(3);
		ListNode n4 = new ListNode(3);
		ListNode n5 = new ListNode(3);
		ListNode n6 = new ListNode(4);
		ListNode n7 = new ListNode(4);
		ListNode n8 = new ListNode(5);

		n1.next = n2;
		n2.next = n3;
		n3.next = n4;
		n4.next = n5;
		n5.next = n6;
		n6.next = n7;
		n7.next = n8;
		System.out.println("调用前");
		printList(n1);
		System.out.println("调用后");
		printList(deleteDuplication(n1));

	}
}
```

## 输出
![image](http://upload-images.jianshu.io/upload_images/11861611-04caa9ff11794509.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)