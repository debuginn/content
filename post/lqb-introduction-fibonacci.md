---
title: "蓝桥杯 入门训练 Fibonacci数列"
date: 2019-01-04T19:20:48+08:00
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

Fibonacci数列的递推公式为：Fn=Fn-1+Fn-2，其中F1=F2=1。

当n比较大时，Fn也非常大，现在我们想知道，Fn除以10007的余数是多少。

## 输入格式

输入包含一个整数n。

## 输出格式

输出一行，包含一个整数，表示Fn除以10007的余数。

> 说明：在本题中，答案是要求Fn除以10007的余数，因此我们只要能算出这个余数即可，而不需要先计算出Fn的准确值，再将计算的结果除以10007取余数，直接计算余数往往比先算出原数再取余简单。

样例输入10

样例输出55

样例输入22

样例输出7704

## 数据规模与约定

1 <= n <= 1,000,000

## C++代码

```c
#include <stdlib.h>
#include <stdio.h>
#define MOD 10007
#define MAXN 1000001
int n, i, F[MAXN];
int main()
{
    scanf("%d", &n);
    F[1] = 1;
    F[2] = 1;
    for (i = 3; i <= n; ++i)
        F[i] = (F[i-1] + F[i-2]) % MOD;
    printf("%d\n", F[n]);
    return 0;
}
```