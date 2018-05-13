---
title: 剑指offer_12
date: 2018-05-12 14:06:27
tags: 剑指offer
categories: 简单算法
---
# 题目：矩阵中的路径
请设计一个函数，用来判断在一个矩阵中是否存在一条包含某字符串所有字符的路径。路径可以从矩阵中任意一格开始，每一步可以在矩阵中间向左、右、上、下移动一格。如果一条路径经过了矩阵的某一格，那么该路径不能再次进入该格子。
<!-- more -->
# 举例说明
在下面的3*4的矩阵中包含一条字符串”bcgkl”的路径。
但矩阵中不包含字符串“abcb”的路径，因为字符串的第一个字符b占据了矩阵中的第一行第二格子之后，路径不能再次进入这个格子。 
a  b  c  d
e  f  g  h 
i  g  k   l
# 思路
## 回溯法
- 回溯法适合解决由**多个步骤组成**的问题，且**每个步骤都有多个选项**。当在某一步选择了其中一个选项，就进入下一步，然后面临新的选项。如果当前的选项已经确定没有正确答案，就回溯到上一步，在上一步选择另一选项，重复进行。
- 回溯法的求解过程一般都可以用树状结构表示出来。**实现代码很适合用递归完成**。
## 本题用回溯法
- 寻找
由于回朔法的递归特性，路径可以被开成一个栈。当在矩阵中定位了路径中前n个字符的位置之后，在与第n个字符对应的格子的周围都没有找到第n+1个字符，这个时候只要在路径上回到第n-1个字符，重新定位第n个字符。
当矩阵中坐标为（row,col）的格子和路径字符串中下标为pathLength的字符一样时，从4个相邻的格子(row,col-1),(row-1,col),(row,col+1)以及(row+1,col)中去定位路径字符串中下标为pathLength+1的字符。  
- 回溯
由于路径不能重复进入矩阵的格子，还需要定义和字符矩阵大小一样的布尔值矩阵，用来标识路径是否已经进入每个格子。 
如果4个相邻的格子都没有匹配字符串中下标为pathLength+1的字符，表明当前路径字符串中下标为pathLength的字符在矩阵中的定位不正确，我们需要回到前一个字符(pathLength-1)，然后重新定位。 
- 一直重复这个过程，直到路径字符串上所有字符都在矩阵中找到合适的位置。

### 代码

```java
public class _12 {
	public static boolean hasPath(char[][] matrix,String str) {
		if(matrix==null||str==null||matrix.length<1||str.length()<1) {
			return false;
		}
		int rows=matrix.length;
		int cols=matrix[0].length;
		//辅助数组初始化
		boolean[][] visitFlag =new boolean[rows][cols];
		for(int row=0;row<rows;row++) {
			for(int col=0;col<cols;col++) {
				visitFlag[row][col]=false;
			}
		}
		//回溯
		for(int row=0;row<rows;row++) {
			for(int col=0;col<cols;col++) {
				if(hasPathCore(matrix,row,col,visitFlag,str,0)) {
					return true;
				}
			}
		}
		return false;
	}
	private static boolean hasPathCore(char[][] matrix,int row,int col,
			boolean[][] visitFlag,String str,int strIdx) {
		if(strIdx>=str.length()) {//完成匹配
			return true;
		}
		if(row<0||row>=matrix.length||col<0||col>=matrix[0].length) {//匹配失败
			return false;
		}
		//1.未被访问 2.字符匹配
		if(!visitFlag[row][col]&&matrix[row][col]==str.charAt(strIdx)) {
			visitFlag[row][col]=true;//被访问  标示
			boolean res=//順時針左上右下
					 hasPathCore(matrix,row,col-1,visitFlag,str,strIdx+1)||
					 hasPathCore(matrix,row-1,col,visitFlag,str,strIdx+1)||
					 hasPathCore(matrix,row,col+1,visitFlag,str,strIdx+1)||
					 hasPathCore(matrix,row+1,col,visitFlag,str,strIdx+1);
			if(res) {//結果对了，跳过后面的 标记修改
				return true;
			}else {
				visitFlag[row][col]=false;
				return false;
			}	
		}else {
			return false;
		}
	}
	public static void main(String[] args) {
		char[][] matrix= {
				{'a','b','c','d'},
				{'e','f','g','h'},
				{'i','j','k','l'}};
		System.out.println(hasPath(matrix,"bcgkl"));//true
		System.out.println(hasPath(matrix,"abcb"));//false
	}
}
```

### 输出
![测试用例输出](http://upload-images.jianshu.io/upload_images/11861611-27579d3ac5355de6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)