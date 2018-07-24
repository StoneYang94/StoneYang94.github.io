---
title: 剑指offer-43
date: 2018-07-22 19:14:16
tags: 剑指offer
categories: 简单算法
---
# 题目：求从1到n的整数中1出现的次数
输入一个整数n求从1 到n这n个整数的十进制表示中1 出现的次数。

<!-- more -->

## 举例说明
例如输入12 ，从1 到12 这些整数中包含1 的数字有`1、10、11 和12`，1 一共出现了5 次。

## 思路

### 一. 对每一个数字进行数字统计---`时间复杂度：O(nlogn)`

数字n有logn位，所以每个数字时间复杂度为O(logn)，n个数字时间复杂度为O(nlogn)。

```java
public  class _43{
    public static int numberOf1Between1AndN(int n){
        int count = 0;
        for(int i=1; i<=n; i++){
            count += get1Nums(i);
        }
        return count ;
    }

    static private int get1Nums(int n){
        int res = 0;
        while(n != 0){
            if(n % 10 == 1){
                res ++;
            }
            n /= 10;
        }
        return res;
    }
    
    public static void main(String[] args) {
		System.out.println("number of '1' form 1 to 12 is " + numberOf1Between1AndN(12));
	}
}
```

![求从1到n的整数中1出现的次数](http://p7vxw6hv7.bkt.clouddn.com/18-7-23/62497710.jpg)

### 二. 寻找规律---`时间复杂度：O(log10 n)`

#### 对于整个 十百千
- 规律
从 1 至 10，在它们的`个`位数中，任意的 X 都出现了 1 次；
从 1 至 100，在它们的`十`位数中，任意的 X 都出现了 10 次；
从 1 至 1000，在它们的`百`位数中，任意的 X 都出现了 100 次。
依此类推，从 1 至 **10<sup>i</sup>**，在它们的此高位——左数第二位（右数第 i 位）中，任意的 X 都出现 **10<sup>i−1</sup>**次。
- 分割
根据设定的整数位置，对n进行分割，分为两部分，`a = n/i`(高位)，`b = n%i`(低位)

#### 例子
1. 位置点i
设定整数点（如1、10、100等等）作为位置点i（对应n的各位、十位、百位等等），分别对每个数位上有多少包含1的点进行分析
2. 分割
根据设定的整数位置，对n进行分割，分为两部分，`a = n/i`(高位)，`b = n%i`(低位)
3. 分析
- 当i表示百位，且百位对应的数>1
如n=31456,i=100，则a=314,b=56，此时百位为1的次数有a/10+1=32（最高两位0~31），每一次都包含100个连续的点，即共有`(a%10+1)*100`个点的百位为1
- 当i表示百位，且百位对应的数为1
如n=31156,i=100，则a=311,b=56，此时百位对应的就是1，则共有a%10(最高两位0-30)次是包含100个连续点，当最高两位为31（即a=311），本次只对应局部点00~56，共b+1次，所有点加起来共有`（a%10*100）+(b+1)`，这些点百位对应为1
- 当i表示百位，且百位对应的数为0
,如n=31056,i=100，则a=310,b=56，此时百位为1的次数有`a/10`=31（最高两位0~30）

# 代码实现

```java
public  class _43{//正常逻辑
    public static int numberOf1Between1AndN(int n){
        int count= 0;
        for (int i = 1; i <= n; i *= 10) {//m表示个十百千
            int a = n/i, b = n % i;
            if(a % 10 == 0){// 0（<X）
                count+= a / 10 * i;   
            }else if(a % 10 == 1) {// 1（=X）
                count+= (a / 10* i) + (b+1);
            }else{// >1（>X）
                count+= (a/10+1)* i;
            }
        }
        return count;

    public static void main(String[] args) {
        System.out.println("number of '1' form 1 to 12 is " + numberOf1Between1AndN(12));
    }
}
```

![求从1到n的整数中1出现的次数](http://p7vxw6hv7.bkt.clouddn.com/18-7-23/62497710.jpg)

```java
public  class _43{
	public static int numberOf1Between1AndN(int n) {
	    int count = 0;
	    for (int i = 1; i <= n; i *= 10) {
	        int a = n / i,b = n % i;
	        //之所以补8，是因为当百位为0，则a/10==(a+8)/10，
	        //当百位>=2，补8会产生进位位，效果等同于(a/10+1)
	        count += (a + 8) / 10 * i + ((a % 10 == 1) ? b + 1 : 0);
	    }
	    return count;
	}
    
    public static void main(String[] args) {
		System.out.println("number of '1' form 1 to 12 is " + numberOf1Between1AndN(12));
	}
}
```

![求从1到n的整数中1出现的次数](http://p7vxw6hv7.bkt.clouddn.com/18-7-23/62497710.jpg)

参考文章
[剑指offer-整数中1出现的次数（从1到n整数中1出现的次数）](https://blog.csdn.net/huzhigenlaohu/article/details/51779365)
[剑指Offer-43：1~n整数中1出现的次数](https://blog.csdn.net/koala_tree/article/details/79524603)

