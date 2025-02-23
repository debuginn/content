---
title: "蓝桥杯 2019第十届蓝桥杯B组C++ 特别数的和"
date: 2019-04-01T21:49:21+08:00
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

小明对数位中含有2、0、1、9 的数字很感兴趣（不包括前导0），在1 到
40 中这样的数包括1、2、9、10 至32、39 和40，共28 个，他们的和是574。
请问，在1 到n 中，所有这样的数的和是多少？

## 输入格式

输入一行包含两个整数n。

## 输出格式

输出一行，包含一个整数，表示满足条件的数的和。

## 样例输入

40

## 样例输出

574

## 评测用例规模与约定

对于20% 的评测用例，1<= n <= 10。
对于50% 的评测用例，1<= n <=100。
对于80% 的评测用例，1<= n <=1000。
对于所有评测用例， 1<=n <=10000。

```c
#include<cstdio>
#include<cstdlib>
#include<cstring>
#include<cmath>
#include<iostream>
#include<algorithm>
#include<string>
#include<vector>
#include<queue>
#include<map>
#include<set>
using namespace std;
 
bool check(int n)
{
	while(n)
	{
		int t=n%10;
		if(t==2||t==0||t==1||t==9)
			return true;
		n/=10;
	}
	return false;
}
 
int main()
{
	int n,ans=0;
	cin>>n;
	for(int i=1;i<=n;i++)
	{
		if(check(i))
			ans+=i;
	}
	cout<<ans<<endl;
	return 0;
}
```