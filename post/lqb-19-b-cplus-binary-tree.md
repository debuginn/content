---
title: "蓝桥杯 2019第十届蓝桥杯B组C++ 完全二叉树的权值"
date: 2019-04-01T21:52:00+08:00
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
---

## 问题描述

给定一棵包含N 个节点的完全二叉树，树上每个节点都有一个权值，按从
上到下、从左到右的顺序依次是A1, A2,...... AN，如下图所示：

![完全二叉树](https://webp.debuginn.com/202303242155344.png)


现在小明要把相同深度的节点的权值加在一起，他想知道哪个深度的节点
权值之和最大？如果有多个深度的权值和同为最大，请你输出其中最小的深度。

> 注：根的深度是1。

## 输入格式

第一行包含一个整数N。
第二行包含N 个整数A1, A2, ...... AN 。

## 输出格式

输出一个整数代表答案。

## 样例输入

```shell
7
1 6 5 4 3 2 1
```

## 样例输出

```shell
2
```

## 评测用例规模与约定

对于所有评测用例，1 <= N <= 100000，100000 <= Ai <=100000。

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
 
#define INF 0x3f3f3f3f
#define N 100005
 
int num[N]={0};
 
int main()
{
	int n;
	scanf("%d",&n);
	for(int i=0;i<n;i++)
		scanf("%d",&num[i]);
	int ans=1,k=0,max=-INF;
	for(int i=1;i<=ceil(log(n+1)/log(2));i++)
	{
		int sum=0; 
		for(int j=0;j<pow(2,i-1);j++)
			sum+=num[k++];
		if(sum>max)
		{
			max=sum;
			ans=i;
		}
	}
	printf("%d\n",ans);
	return 0;
}
```