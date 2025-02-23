---
title: "蓝桥杯 基础练习 矩阵乘法"
date: 2019-01-22T21:33:56+08:00
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

给定一个N阶矩阵A，输出A的M次幂（M是非负整数）
```　　
例如：
　　A =
　　1 2
　　3 4
　　A的2次幂
　　7 10
　　15 22
```

## 输入格式　　

第一行是一个正整数N、M（1<=N<=30, 0<=M<=5），表示矩阵A的阶数和要求的幂数
　　接下来N行，每行N个绝对值不超过10的非负整数，描述矩阵A的值

## 输出格式　　

输出共N行，每行N个整数，表示A的M次幂所对应的矩阵。相邻的数之间用一个空格隔开

## 样例输入

```shell
2 2
1 2
3 4
```

## 样例输出

```shell
7 10
15 22
```

## C++算法

```c
#include<cstdio>
 #include<iostream>
 #include<cstring>
 using namespace std;
 int a[101][101];
 int c[101][101];
 int ans[101][101];
 int main()
 {
     int i,j,k,l,m,n;
     scanf("%d%d",&n,&m);
     for(i=1;i<=n;i++)
       for(j=1;j<=n;j++)
         scanf("%d",&a[i][j]);
     memset(ans,0,sizeof(ans));
     for(i=1;i<=n;i++) ans[i][i]=1;
     for(k=1;k<=m;k++)
     {    memset(c,0,sizeof(c));
         for(i=1;i<=n;i++)for(j=1;j<=n;j++)for(l=1;l<=n;l++)c[i][j]+=ans[i][l]*a[l][j];
         for(i=1;i<=n;i++)for(j=1;j<=n;j++)ans[i][j]=c[i][j];
     }
     for(i=1;i<=n;i++)
     {
         for(j=1;j<n;j++)printf("%d ",ans[i][j]);
         printf("%d\n",ans[i][n]);
     }
     return 0;
 }
```