---
title: "蓝桥杯 基础练习 十六进制转八进制"
date: 2019-01-16T21:45:32+08:00
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

给定n个十六进制正整数，输出它们对应的八进制数。

## 输入格式

输入的第一行为一个正整数n （1<=n<=10）。
　
接下来n行，每行一个由0~9、大写字母A~F组成的字符串，表示要转换的十六进制正整数，每个十六进制数长度不超过100000。

## 输出格式

输出n行，每行为输入对应的八进制正整数。

>【注意】
　　输入的十六进制数不会有前导0，比如012A。
　　输出的八进制数也不能有前导0。

## 样例输入

　　2
　　39
　　123ABC

## 样例输出
　　71
　　4435274

>【提示】
　　先将十六进制数转换成某进制数，再由某进制数转换成八进制。

## C++算法

```c
#include <iostream>
#include <stdio.h>
#include <string.h>
#include <STDLIB.H>
/* run this program using the console pauser or add your own getch, system("pause") or input loop */

int GetI(char c)
{
	return c>>4&1?c&15:(c&15)+9; 
}

int main(int argc, char *argv[]) {
	char arr[200001] = {'\0'};
	char brr[400001] = {'\0'};
	int n = 0;
	int i = 0;
	scanf("%d",&n);

	for(i = 0;i < n;i++)
	{
		scanf("%s",arr);
		int m[3] = {1,16,256};
		int len = strlen(arr);
		int j = len-1;
		int a,b,c;
		a = b = c = 0;
		int k = 0,l = 0;
		int count = 0;
		while(j>-1)
		{

			a += (arr[j]>>4&1?arr[j]&15:(arr[j]&15)+9)*m[k]; //个位
			if(k==2||j==0)
			{
				while(a)
				{
					brr[l++] = ((a&7)|48);
					a = a>>3;
					count++;
				}
				while(j!=0&&count<4)
				{
					brr[l++] = '0';
					count++;
				}
				count = 0;
			}
			k = (k+1)%3;
			j--;
		}
		strrev(brr);
		printf("%s\n",brr);
		memset(arr,'\0',(sizeof(char)*200001));
		memset(brr,'\0',(sizeof(char)*400001));
	}
    return 0; 
}
```
