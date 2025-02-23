---
title: "蓝桥杯 基础练习 回形取数"
date: 2019-02-01T22:30:00+08:00
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

回形取数就是沿矩阵的边取数，若当前方向上无数可取或已经取过，则左转90度。一开始位于矩阵左上角，方向向下。

## 输入格式

输入第一行是两个不超过200的正整数m, n，表示矩阵的行和列。接下来m行每行n个整数，表示这个矩阵。

## 输出格式

输出只有一行，共mn个数，为输入矩阵回形取数得到的结果。数之间用一个空格分隔，行末不要有多余的空格。

## 样例输入

```shell
3 3
1 2 3
4 5 6
7 8 9
```

## 样例输出

```shell
1 4 7
8 9 6
3 2 5
```

## 样例输入

```shell
3 2
1 2
3 4
5 6
```

## 样例输出

`1 3 5 6 4 2`

## C++算法

```c
#include<stdio.h>
int main()
{
	int m,n;
	scanf("%d %d",&m,&n);
	int s[200][200];
	int a[200][200];
	int i,j;
	for(i=0;i<m;i++)
		for(j=0;j<n;j++)
		{
			scanf("%d",&s[i][j]);
			a[i][j]=-1;
		}
	int k=0,b=m-1,c=n-1;
	int h=0;
	for(i=j=h;a[i][j]==-1&&k<=m*n;)
		{
			if(k<m*n)
				printf("%d ",s[i][j]);
			else
				printf("%d",s[i][j]);
			k++;
			a[i][j]=0;
			if(i<b&&a[i+1][j]==-1&&j==n-1-c)
			{
				i++;
				continue;
			}	
			if(i==b&&a[i][j+1]==-1)
			{
				j++;
				continue;
			}	
			if(j==c&&a[i-1][j]==-1)
			{
				i--;
				continue;
			}
			if(i==m-1-b&&a[i][j-1]==-1)
			{
				j--;
				continue;
			}
			i=j=(++h);
			b--;c--;
		} 
	return 0;
} 
```