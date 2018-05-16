---
title: 剑指offer_19
date: 2018-05-16 19:55:22
tags: 剑指offer
categories: 简单算法
---
# 题目：正则表达式匹配
请实现一个函数用来匹配包括’.’和’ * ‘的正则表达式。模式中的字符’.’表示任意一个字符，而’ * ‘表示它前面的字符可以出现任意次（包含0次）。 在本题中，匹配是指**字符串的所有字符**匹配整个**模式**。
<!-- more -->
# 举例说明
例如，字符串”aaa”与模式”a.a”和”ab * ac * a”匹配，但与”aa.a”和”ab * a”均不匹配
# 思路
- 递归实现
- 分清模式字符串pattern和待匹配字符串Str。匹配是指Str的所有字符匹配整个pattern。
- 每次从Str里拿出一个字符和pattern中的字符去匹配，主要的核心是判断pattern当前字符是否是"*"。
- 设匹配递归函数 match(str, pattern)
对每一个字符
1. 一对一匹配 ：如果模式匹配字符的下一个字符不是"*"
1）如果pattern中的字符ch是’.’，那么它可以匹配Str中的任意字符；
2）如果pattern中的字符ch不是’.’，但Str中的字符是ch，则它们同样相互匹配
匹配则在Str和pattern上都向后移动一个字符，也就是match(str+1, pattern+1)，接着匹配后面的字符。
否则则直接返回false。
2. 一对多匹配：如果模式匹配字符的下一个字符是"*"
  - 如果pttern当前字符和str的当前字符匹配，：有以下三种可能情况
1）pttern当前字符能匹配 str 中的 0 个字符：match(str, pattern+2)
2）pttern当前字符能匹配 str 中的 1 个字符：match(str+1, pattern+2)
3）pttern当前字符能匹配 str 中的 多 个字符：match(str+1, pattern)
  - 如果pttern当前字符和和str的当前字符不匹配
pttern当前字符能匹配 str 中的 0 个字符：match(str, pattern+2)
## 代码

```java
public class _19 {
	public static boolean match(String str, String pattern) {
		if (str == null || pattern == null) {
			return false;
		}
		return matchCore(str, 0, pattern, 0);
	}

	private static boolean matchCore(String str, int s, String pattern, int p) {
		// str和pattern都到达尾，说明成功匹配
		if (s >= str.length() && p >= pattern.length()) {
			return true;
		}
		// 只有pattern到达结尾，说明匹配失败
		if (s != str.length() && p >= pattern.length()) {
			return false;
		}
		//pattern未结束，str有可能结束有可能未结束
		//1.下一个是不是*  2.str是否结束   （3.当前字符是否匹配）
		// p位置的下一个字符中为*号-----------------------------------------
		if (p + 1 < pattern.length() && pattern.charAt(p + 1) == '*') {
			// str已经结束
			if (s >= str.length()) {
				return matchCore(str, s, pattern, p + 2);
			}
			// str还没有结束
			else {//当前pattern与当前str 字符匹配
				if (pattern.charAt(p) == str.charAt(s) || pattern.charAt(p) == '.') {
					return
							// 当前pattern匹配当前str 0个字符，如aa*a与aa
							    matchCore(str, s, pattern, p + 2)
					        // 当前pattern匹配当前str 1个字符，如ab*a与aba
					     	||  matchCore(str, s + 1, pattern, p + 2)
							//当前pattern匹配当前str 多个字符，如ab*a与aaba
							||  matchCore(str, s + 1, pattern, p);
				} else {//当前pattern与当前str 字符不匹配 如a*与b
					return matchCore(str, s, pattern, p + 2);
				}
			}
		}
		// p位置的下一个字符中不是*号--------------------------------
		// str已经结束
		if (s >= str.length()) {
			return false;
		}
		// str还没有结束
		else {
			if (str.charAt(s) == pattern.charAt(p) || pattern.charAt(p) == '.') {
				return matchCore(str, s + 1, pattern, p + 1);
			}
		}
		return false;
	}

	public static void main(String[] args) {
		System.out.println(match("aaa", "ab*ac*a"));//true
		System.out.println(match("aaba", "ab*a*c*a"));//false
		System.out.println(match("bbbba", ".*a*a"));//true
		System.out.println(match("bcbbabab", ".*a*a"));//false
	}
}
```

## 输出
![image](http://upload-images.jianshu.io/upload_images/11861611-767d3194713813d8.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


# 相关学习
[正则表达式手册](http://tool.oschina.net/uploads/apidocs/jquery/regexp.html)

