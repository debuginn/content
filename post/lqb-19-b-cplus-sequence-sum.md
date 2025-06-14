---
title: "蓝桥杯 2019第十届蓝桥杯B组C++ 数列求值"
date: 2019-04-01T19:35:21+08:00
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

给定数列 1, 1, 1, 3, 5, 9, 17, …，从第 4 项开始，每项都是前 3 项的和。求第 20190324 项的最后 4 位数字。

## 答案提交

这是一道结果填空的题，你只需要算出结果后提交即可。本题的结果为一 个 4 位整数（提示：答案的千位不为 0），在提交答案时只填写这个整数，填写多余的内容将无法得分。

## 答案

4659

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

#define MOD 10000
 
int num[20190324]={1,1,1};
 
int main()
{
	for(int i=3;i<20190324;i++)
	{
		num[i]=(num[i-3]+num[i-2])%MOD;
		num[i]=(num[i-1]+num[i])%MOD; 
	}
	cout<<num[20190323]<<endl;
	return 0;
}
```
