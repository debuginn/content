---
title: "蓝桥杯 基础练习 特殊的数字"
date: 2019-01-11T19:00:36+08:00
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

153是一个非常特殊的数，它等于它的每位数字的立方和，即153=1*1*1+5*5*5+3*3*3。编程求所有满足这种条件的三位十进制数。

## 输出格式　　

按从小到大的顺序输出满足条件的三位十进制数，每个数占一行。

## C++算法

```c
#include<iostream>
using namespace std;
int main()
{
    int i,j,k;
    for(i=1;i<=9;i++)
    {
        for(j=0;j<=9;j++)
        {
            for(k=0;k<=9;k++)
            {
                if(i*100+j*10+k==i*i*i+j*j*j+k*k*k)
                {
                    cout<<i*100+j*10+k<<endl;
                }
            }
        }
    }
    return 0;
}
```