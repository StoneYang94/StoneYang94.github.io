---
title: 剑指offer_20
date: 2018-05-17 18:53:42
tags: 剑指offer
categories: 简单算法
---
# 题目：表示数值的字符串
请实现一个函数用来判断字符串是否表示数值（包括整数和小数）。

<!-- more -->

# 举例说明
例如，字符串”+100”,”5e2”,”-123”,”3.1416”和”-1E-16”都表示数值。 但是”12e”,”1a3.14”,”1.2.3”,”+-5”和”12e+4.3”都不是。
- 正负号只能出现在数字最前面或者指数部分（如果有）的指数系数前，且每个地方的正负号至多一个
- 可以没有整数部分只有小数部分
- 指数部分（如果有）的指数必须有，且必须是整数

# 思路

## 正则表达式法

[正则表达式手册](http://tool.oschina.net/uploads/apidocs/jquery/regexp.html)

### 代码

```java
public class _20 {
	public static boolean isNumeric(String str) {
		return str.matches(	"^[+-]?[0-9]*[.]?[0-9]*([eE][+-]?[0-9]+)?$");
	}

	public static void main(String[] args) {
		System.out.println(isNumeric("100"));// true
		System.out.println(isNumeric("600."));// true
		System.out.println(isNumeric("3.1416"));// true
		System.out.println(isNumeric("-.123"));// true
		System.out.println(isNumeric("123.45e+6"));// true
		System.out.println(isNumeric("5e2"));// true
		System.out.println(isNumeric("-1E-16"));// true
		System.out.println();
		System.out.println(isNumeric("12e"));// false
		System.out.println(isNumeric("1a3.14"));// false
		System.out.println(isNumeric("1+23"));// false
		System.out.println(isNumeric("1.2.3"));// false
		System.out.println(isNumeric("+-5"));// false
		System.out.println(isNumeric("12e+5.4"));// false
	}
}
```

### 输出
![image](http://upload-images.jianshu.io/upload_images/11861611-d1fed566ff0a32dc.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 逐步判断法
- 逻辑见代码注释
- **每次索引后移后先判断是否到达末尾**。即每一次后移index，都要判断str是否到尾端
### 代码

```java
public class _20 {
	//分几步1.+-号  2.跳过纯数字  3.遇到小数点就跳过，继续扫描纯数字  4.遇到指数符号就判断
	/*关键：每一次后移index，都要判断字符串是否到尾端
	 * 	index++;
	 *   ...
	 * 	if (index >= string.length()) {
			...   ;
		}
	 */
	public static boolean isNumeric(String string) {
		if (string == null || string.length() < 1) {
			return false;
		}
		int index = 0;
		//1.----------------------------------------------------
		if (string.charAt(index) == '+' || string.charAt(index) == '-') {
			index++;
		}
		if (index >= string.length()) {
			return false;
		}
		//2.-----------------------------------------------------
		boolean numeric = true;
		index = skipDigits(string, index);
		if (index < string.length()) {
		//3.-----------------------------------------------------
			if (string.charAt(index) == '.') {
				index++;
				index = skipDigits(string, index);
				if (index >= string.length()) {
					numeric = true;
				}
				//4.----------------------------
				else if (index < string.length() && (string.charAt(index) == 'e' || string.charAt(index) == 'E')) {
					numeric = isExponential(string, index);
				} else {
					numeric = false;
				}
			}
			// 4.--------------------------------------
			else if (string.charAt(index) == 'e' || string.charAt(index) == 'E') {
				numeric = isExponential(string, index);
			} else {
				numeric = false;
			}

			return numeric;
		}
		// 跳过了+-号和纯数字，就到尾了
		else {
			return true;
		}

	}

	//e(E)后面接整数 ，中间可以有一个正负号：如E5，e5，E+5，e-5
	//分三步 1.判断eE  2.判断+-  3.跳过纯数字直到末尾
	private static boolean isExponential(String str, int index) {
		//1-----------------------------------
		if (index >= str.length() || (str.charAt(index) != 'e' && str.charAt(index) != 'E')) {
			return false;
		}
		index++;
		if (index >= str.length()) {
			return false;
		}
		//2----------------------------------------
		if (str.charAt(index) == '+' || str.charAt(index) == '-') {
			index++;
		}
		if (index >= str.length()) {
			return false;
		}
		//3----------------------------------------------------
		index = skipDigits(str, index);
		// 如果已经处理到了的数字的末尾就认为是正确的指数
		return index >= str.length();
	}

	// 跳过字符串部分的纯数字部分
	private static int skipDigits(String str , int index) {
		while (index < str.length() && str.charAt(index) >= '0' && str.charAt(index) <= '9') {
			index++;
		}
		return index;
	}

	public static void main(String[] args) {
		System.out.println(isNumeric("100"));// true
		System.out.println(isNumeric("600."));// true
		System.out.println(isNumeric("3.1416"));// true
		System.out.println(isNumeric("-.123"));// true
		System.out.println(isNumeric("123.45e+6"));// true
		System.out.println(isNumeric("5e2"));// true
		System.out.println(isNumeric("-1E-16"));// true
		System.out.println();
		System.out.println(isNumeric("12e"));// false
		System.out.println(isNumeric("1a3.14"));// false
		System.out.println(isNumeric("1+23"));// false
		System.out.println(isNumeric("1.2.3"));// false
		System.out.println(isNumeric("+-5"));// false
		System.out.println(isNumeric("12e+5.4"));// false
	}
}
```

### 输出
![image](http://upload-images.jianshu.io/upload_images/11861611-99d4b01b761576e6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)