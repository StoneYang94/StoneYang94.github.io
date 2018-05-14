---
title: 剑指offer_13
date: 2018-05-14 19:13:16
tags: 剑指offer
categories: 简单算法
---
# 题目：机器人的运动范围
地上有个m行n列的方格。一个机器人从坐标(0,0)的格子开始移动，它每一次可以向左、右、上、下移动一格，但不能进入行坐标和列坐标的数位之和大于k的格子。
<!-- more -->
# 举例说明：
　例如，当k为18时，机器人能够进入方格(35,37)，因为3+5+3+7=18。但它不能进入方格(35,38)，因为3+5+3+8=19。请问该机器人能够达到多少格子？
# 思路
- 与12题类似，机器人从坐标(0,0)开始移动。当它准备进入坐标为(i,j)的格子时，通过检查坐标的数位和来判断机器人是否能够进入。如果机器人能够进入坐标为(i,j)的格子，我们接着再判断它能否进入四个相邻的格子(i,j-1)、(i-1,j),(i,j+1)和(i+1,j)。
- 不同的是准入条件的变化，方格中的内容不再重要，仅需要方格的长和宽范围即可。
- 故“只停不退”，不用重置辅助数组对应位置的布尔值。
## 代码

```java
public class _13 {
	public static int movingCount(int threshold,int rows,int cols) {
		if(threshold<0||rows<1||cols<1) {
			return 0;
		}
		//初始化辅助数组
		boolean[] visitFlag=new boolean[rows*cols];
		for(int i=0;i<visitFlag.length;i++) {
				visitFlag[i]=false;
			}
		return movingCountCore(threshold,rows,cols,0,0,visitFlag);
	}
	private static int movingCountCore(int threshold,int rows,int cols,int row,int col,boolean[] visitFlag) {
		int count=0;
		//达到限制的准入条件
		if(check(threshold,rows,cols,row,col,visitFlag)) {
			visitFlag[row*cols+col]=true;//标记为已经进入，但不同于12题，之后不用回溯，所以只停不退
			count=1+movingCountCore(threshold, rows, cols, row-1, col, visitFlag)
			       +movingCountCore(threshold, rows, cols, row, col-1, visitFlag)
			       +movingCountCore(threshold, rows, cols, row+1, col, visitFlag)
			       +movingCountCore(threshold, rows, cols, row, col+1, visitFlag);
		}
		return count;
	}
	private static boolean check(int threshold,int rows,int cols,int row,int col,boolean[] visitFlag) {
		return col>=0 && col<cols && row>=0 && row<rows
				&& !visitFlag[row*cols+col]
				&& (getDigitSum(row)+getDigitSum(col)<=threshold);
	}
	private static int getDigitSum(int number) {
		int sum=0;
		while(number>0) {
			sum=number%10;
			number/=10;
		}
		return sum;
	}
	public static void main(String[] args) {
		System.out.println(movingCount(3,3,3));//8,功能测试
		System.out.println(movingCount(0,1,1));//1,边界测试
		System.out.println(movingCount(-3,3,3));//0,非法输入测试
	}
}
```

## 输出
![image](http://upload-images.jianshu.io/upload_images/11861611-ce1d06d33f085831.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)