---
title: "软件质量测试 等价类划分 三角形问题"
date: 2019-07-03T18:18:38+08:00
keywords: "软件质量测试"
comments: true
tags: ["软件质量测试"]
categories: ["software"]
image: "https://webp.debuginn.com/202303191412328.jpg"
---

## 问题描述

一个程序读入3个整数，把这三个数值看作一个三角形的3条边的长度值。这个程序要打印出信息，说明这个三角形是不等边的、是等腰的、还是等边的。

我们可以设三角形的3条边分别为A，B，C。如果它们能够构成三角形的3条边，必须满足：
A>0，B>0，C>0，且A+B>C，B+C>A，A+C>B。
如果是等腰的，还要判断A=B，或B=C，或A=C。
如果是等边的，则需判断是否A=B，且B=C，且A=C。

## 等价类划分

![判定关系表](https://webp.debuginn.com/202303191420550.png)

![等价类划分表](https://webp.debuginn.com/202303191421993.png)

## 代码实现

```python
float a, b, c; 
 
printf("请输入三角形三边"); 
 scanf("%f,%f,%f",&a,&b,&c); 
 if (a==b||b==c||a==c)  printf("等腰三角形"); 
 if (a==b&&b==c)  printf("等边三角形"); 
 
if (a*a+b*b==c*c||a*a+c*c==b*b||b*b+c*c==a*a)  printf("直角三角形"); 
 else  
printf("普通三角形");
```

转载自文章： [软件测试-三角形问题](https://www.cnblogs.com/youxin/p/3516821.html) 