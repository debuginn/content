---
title: "蓝桥杯 基础练习 分解质因数"
date: 2019-01-20T21:36:43+08:00
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

求出区间`[a,b]`中所有整数的质因数分解。

## 输入格式

输入两个整数a，b。

## 输出格式　

每行输出一个数的分解，形如`k=a1*a2*a3…(a1<=a2<=a3…，k也是从小到大的)`(具体可看样例)

## 样例输入

3 10

## 样例输出

```shell
3=3
4=2*2
5=5
6=2*3
7=7
8=2*2*2
9=3*3
10=2*5
```

> 提示 先筛出所有素数，然后再分解。

## 数据规模和约定　　

2<=a<=b<=10000

## C++算法

```c
#include<stdio.h>
#include<iostream>
#include<string.h>
#include<string>
#include <ctype.h> 
#include <math.h>  
using namespace std; 
int factor(int n)  
{  
    int i, j = (int)sqrt(n);  
    if (n % 2 == 0) return 2;  
    for (i = 3; i <= j; i++)  
        if (n % i == 0) return i;  
    return n;  
}  
  
int main()  
{  
    int i, j, k, m, n;  
    scanf("%d%d", &m, &n);  
    for (i = m; i <= n; i++)  
    {  
        j = factor(i);  
        k = i / j;  
        printf("%d=%d", i, j);  
        while (k > 1)
        {
            j = factor(k);
            k /= j;  
            printf("*%d", j);  
        }  
        printf("\n");  
    }  
    return 0;  
}  
```