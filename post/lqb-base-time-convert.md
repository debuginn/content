---
title: "蓝桥杯 基础练习 时间转换"
date: 2019-01-18T21:42:07+08:00
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

给定一个以秒为单位的时间t，要求用“<H>:<M>:<S>”的格式来表示这个时间。<H>表示时间，<M>表示分钟，而<S>表示秒，它们都是整数且没有前导的“0”。

例如，若t=0，则应输出是“0:0:0”；若t=3661，则输出“1:1:1”。

## 输入格式　　

输入只有一行，是一个整数t（0<=t<=86399）。

## 输出格式　　

输出只有一行，是以“<H>:<M>:<S>”的格式所表示的时间，不包括引号。

样例输入0

样例输出0:0:0

样例输入5436

样例输出1:30:36

## C++算法

```c
#include <iostream>
using namespace std;
int main()
{
	int n,H,M,S,t;
	cin>>n;
	H=n/3600;
	t=n%3600;
	M=t/60;
	S=t%60;
	cout<<H<<":"<<M<<":"<<S;	
}

```