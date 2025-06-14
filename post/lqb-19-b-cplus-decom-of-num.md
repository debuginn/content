---
title: "蓝桥杯 2019第十届蓝桥杯B组C++ 数的分解"
date: 2019-04-01T21:42:09+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "蓝桥杯"
comments: true
tags: ["蓝桥杯","algorithm"]
categories: ["algorithm"]
image: "https://static.debuginn.com/202303241303887.jpg"
---

## 问题描述

把 2019 分解成 3 个各不相同的正整数之和，并且要求每个正整数都不包含数字 2 和 4，一共有多少种不同的分解方法？
注意交换 3 个整数的顺序被视为同一种方法，例如 1000+1001+18 和 1001+1000+18 被视为同一种。

## 答案提交

这是一道结果填空的题，你只需要算出结果后提交即可。本题的结果为一 个整数，在提交答案时只填写这个整数，填写多余的内容将无法得分。

## 答案

40785

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
		if(n%10==2||n%10==4)
			return false;
		n/=10;
	}
	return true;
}
 
int main()
{
	int ans=0;
	for(int i=1;i<2019;i++)
	{
		if(!check(i))
			continue;
		for(int j=i+1;j<2019;j++)
		{
			if(!check(j))
				continue;
			for(int k=j+1;k<2019;k++)
			{
				if(!check(k))
					continue;
				if(i+j+k==2019)
					ans++; 
			}
		} 
	}
	cout<<ans<<endl;
	return 0;
}
```