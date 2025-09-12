---
title: "蓝桥杯 2019第十届蓝桥杯B组C++ 等差数列"
date: 2019-04-01T21:59:12+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "蓝桥杯"
comments: true
tags: ["蓝桥杯","algorithm"]
categories: ["algorithm"]
image: "https://webp.debuginn.com/202303241303887.jpg"
---

## 问题描述

数学老师给小明出了一道等差数列求和的题目。但是粗心的小明忘记了一
部分的数列，只记得其中N 个整数。
现在给出这N 个整数，小明想知道包含这N 个整数的最短的等差数列有几项？

## 输入格式

输入的第一行包含一个整数N。
第二行包含N 个整数A1; A2; ...... ; AN。(注意A1 ~AN 并不一定是按等差数
列中的顺序给出)

## 输出格式

输出一个整数表示答案。

## 样例输入

```shell
5
2 6 4 10 20
```

## 样例输出

```shell
10
```

## 样例说明

包含2、6、4、10、20 的最短的等差数列是2、4、6、8、10、12、14、16、
18、20。

## 评测用例规模与约定

对于所有评测用例，2 <= N <= 100000，0 <= Ai <= 109。

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
 
#define N 100005
 
int num[N]={0},d[N]={0};
 
int gcd(int a,int b)
{
	return (b>0)?gcd(b,a%b):a;
}
 
int main()
{
	int n;
	scanf("%d",&n);
	for(int i=0;i<n;i++)
		scanf("%d",&num[i]);
	sort(num,num+n);
        bool zero=false;
	for(int i=0;i<n-1;i++)
	{
	        d[i]=num[i+1]-num[i];
                if(d[i]==0)
                {
                        zero=true;
                        break;
                }
        }
        if(zero)//常数数列
                printf("%d\n",n);
        else
        {
	        int mind=gcd(d[0],d[1]);
	        for(int i=2;i<n-1;i++)
		        mind=gcd(mind,d[i]);
	        printf("%d\n",(num[n-1]-num[0])/mind+1);
        }
	return 0;
}
```