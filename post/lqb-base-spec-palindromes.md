---
title: "蓝桥杯 基础练习 特殊回文数"
date: 2019-01-13T18:56:54+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "蓝桥杯"
comments: true
weight: 0

tags: ["蓝桥杯","algorithm"]
categories: ["algorithm"]


image: "https://webp.debuginn.com/202303241303887.jpg"
imagePreview: "https://webp.debuginn.com/202303241303887.jpg"
---

## 问题描述　　

123321是一个非常特殊的数，它从左边读和从右边读是一样的。

输入一个正整数n， 编程求所有这样的五位和六位十进制数，满足各位数字之和等于n 。

## 输入格式　　

输入一行，包含一个正整数n。

## 输出格式　　

按从小到大的顺序输出满足条件的整数，每个整数占一行。

## 样例输入

52

## 样例输出

899998
989989
998899

## 数据规模和约定　　

1<=n<=54。

## C++算法

```c
#include <iostream>
using namespace std;
int main()
{
	int n,a,b,c,t;
	cin>>n;
	for(a=1;a<10;a++)
	for(b=0;b<10;b++)
	for(c=0;c<10;c++)
	{
		t=a*10001+b*1010+c*100;
		if(2*a+2*b+c==n)
			cout<<t<<endl;	
	}
	for(a=1;a<10;a++)
	for(b=0;b<10;b++)
	for(c=0;c<10;c++)
	{
		t=a*100001+b*10010+c*1100;
		if(2*a+2*b+2*c==n)
			cout<<t<<endl;	
	}
	return 0;
}
```
