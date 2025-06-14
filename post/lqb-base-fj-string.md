---
title: "蓝桥杯 基础练习 FJ的字符串"
date: 2019-01-30T22:40:48+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "蓝桥杯"
comments: true
weight: 0

tags: ["蓝桥杯","algorithm"]
categories: ["algorithm"]


image: "https://static.debuginn.com/202303241303887.jpg"
imagePreview: "https://static.debuginn.com/202303241303887.jpg"
---

## 问题描述

FJ在沙盘上写了这样一些字符串：

A1 = “A”

A2 = “ABA”
　　
A3 = “ABACABA”
　　
A4 = “ABACABADABACABA”
　　
… …
　　
你能找出其中的规律并写所有的数列AN吗？

## 输入格式

仅有一个数：N ≤ 26。

## 输出格式

请输出相应的字符串AN，以一个换行符结束。输出中不得含有多余的空格或换行、回车符。

## 样例输入

3

## 样例输出

ABACABA

## C++算法

```c
#include<iostream>
#include<cstdio>
using namespace std;
void dfs(int k,int p)
{
	if (k==1)
	{
	   printf("%c",p+'A');
	   return;	
	}
	dfs(k/2,p-1);dfs(1,p);dfs(k/2,p-1);
}
int main()
{
     int n;
     scanf("%d",&n);
     int sum=1;
     n--;
     for (int i=1;i<=n;i++) sum=sum*2+1;
	 dfs(sum,n);	
	 return 0;
}
```