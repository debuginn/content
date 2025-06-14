---
title: "蓝桥杯 基础练习 Sine之舞"
date: 2019-01-30T22:42:41+08:00
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

最近FJ为他的奶牛们开设了数学分析课，FJ知道若要学好这门课，必须有一个好的三角函数基本功。所以他准备和奶牛们做一个“Sine之舞”的游戏，寓教于乐，提高奶牛们的计算能力。

不妨设

```shell
An=sin(1–sin(2+sin(3–sin(4+…sin(n))…)
Sn=(…(A1+n)A2+n-1)A3+…+2)An+1
```
FJ想让奶牛们计算Sn的值，请你帮助FJ打印出Sn的完整表达式，以方便奶牛们做题。

## 输入格式

仅有一个数：N<201。

## 输出格式

请输出相应的表达式Sn，以一个换行符结束。输出中不得含有多余的空格或换行、回车符。

## 样例输入

3

## 样例输出

`((sin(1)+3)sin(1–sin(2))+2)sin(1–sin(2+sin(3)))+1`

## C++算法

```c
#include<stdio.h>
void An_Output(int n, int t)
{
	if(n == t)
	{
		printf("sin(%d)", t);
		return ;
	}
	char c;
	c = t % 2 == 1 ? '+' : '-';
	printf("sin(%d%c", t, c);
	An_Output(n, ++t);
	printf(")");
}
void Sn_Output(int n, int t)
{
	//　Sn=(...(A1+n)A2+n-1)A3+...+2)An+1
	if(n == t)
	{
		return ;
	}
	printf("(");
	Sn_Output(n, t+1);
	if(t != n - 1)
	{	
		printf(")");
	}
	An_Output(n - t, 1);
	printf("+%d", t+1);
}
int main()
{
	int n;
	scanf("%d", &n);
	Sn_Output(n, 1);
	if(n!=1)
		printf(")");
	An_Output(n, 1);
	printf("+1\n");
	return 0;
}
```
