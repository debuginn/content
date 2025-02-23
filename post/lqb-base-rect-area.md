---
title: "蓝桥杯 基础练习 矩形面积交"
date: 2019-01-23T21:31:42+08:00
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

平面上有两个矩形，它们的边平行于直角坐标系的X轴或Y轴。对于每个矩形，我们给出它的一对相对顶点的坐标，请你编程算出两个矩形的交的面积。

## 输入格式　　

输入仅包含两行，每行描述一个矩形。

在每行中，给出矩形的一对相对顶点的坐标，每个点的坐标都用两个绝对值不超过10^7的实数表示。

## 输出格式　　

输出仅包含一个实数，为交的面积，保留到小数后两位。

## 样例输入

```shell
1 1 3 3
2 2 4 4
```

## 样例输出

1.00

## C++算法

```c
#include <iostream>
#include <algorithm>
#include <cmath>
#include <cstdio>
using namespace std;
int main()
{
    double x1,x2,y1,y2;
    double q1,q2,w1,w2;
    while(cin>>x1>>y1>>x2>>y2>>q1>>w1>>q2>>w2)
    {
        double xx=max(min(x1,x2),min(q1,q2));
        double yy=max(min(y1,y2),min(w1,w2));
        double xxup=min(max(x1,x2),max(q1,q2));
        double yyup=min(max(y1,y2),max(w1,w2));
        if(xxup>xx)
        printf("%.2f\n",fabs((xx)-(xxup))*fabs((yy)-(yyup)));
        else printf("0.00\n");
    }
}
```