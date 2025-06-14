---
title: "蓝桥杯 基础练习 阶乘计算"
date: 2019-02-07T21:59:28+08:00
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

输入一个正整数n，输出n!的值。

其中`n!=1*2*3*…*n`。

## 算法描述

n!可能很大，而计算机能表示的整数范围有限，需要使用高精度计算的方法。使用一个数组A来表示一个大整数a，`A[0]`表示a的个位，`A[1]`表示a的十位，依次类推。 

将a乘以一个整数k变为将数组A的每一个元素都乘以k，请注意处理相应的进位。 

首先将a设为1，然后乘2，乘3，当乘到n时，即得到了n!的值。

## 输入格式

输入包含一个正整数n，n<=1000。

## 输出格式

输出`n!`的准确值。

## 样例输入

10

## 样例输出

3628800

## C++算法

```c
#include <stdio.h>
#include <string.h>
#define MAX 10000
#define mod 10000
#define baselen 4
#define in(a) scanf("%d",&a)
#define out1(a) printf("%d",a)
#define out2(a) printf("%04d",a)
typedef int type;
struct bint{
	type dig[MAX], len;
	bint(){len = 0, dig[0] = 0;}
};
void by(bint a, type b, bint& c){
	type i, carry;
	for( i = carry = 0; i <= a.len || carry; i++){
		if( i <= a.len ) carry += b*a.dig[i];
		c.dig[i] = carry%mod;
		carry /= mod;
	}
	i--;
	while( i && !c.dig[i] )i--;
	c.len = i;
}
bool input(bint& a){
	type i, j, w, k, p;
	char data[MAX*baselen+1];
	if(scanf("%s",data)==EOF)return false;
	w = strlen(data) - 1, a.len = 0;
	for(p=0;p<=w&&data[p]=='0';p++);
	while(1){
		i = j = 0, k = 1;
		while(i<baselen&&w>=p){
			j = j+ (data[w--] - '0')*k;
			k *= 10, i++;
		}
		a.dig[a.len++] = j;
		if(w<p)break;
	}
	a.len--;
	return true;
}
void output(bint& a){
	type i;
	i = a.len - 1;
	out1(a.dig[a.len]);
	while(i>=0)out2(a.dig[i--]);
}
void give(type a, bint& b){
	b.dig[0] = a%mod;
	a /= mod;
	if(a>0)b.dig[1] = a, b.len = 1;
	else b.len = 0;
}
int main()
{
	bint a;int b,i;scanf("%d",&b);give(1,a);
	for(i=2;i<=b;i++)by(a,i,a);
	output(a);printf("\n");
	return 0;
}
```
