---
title: "蓝桥杯 基础练习 数列排序"
date: 2019-01-17T21:43:51+08:00
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

给定一个长度为n的数列，将这个数列按从小到大的顺序排列。1<=n<=200

## 输入格式　　

第一行为一个整数n。
第二行包含n个整数，为待排序的数，每个整数的绝对值小于10000。

## 输出格式　　

输出一行，按从小到大的顺序输出排序后的数列。

## 样例输入

58 3 6 4 9

## 样例输出

3 4 6 8 9

## C++算法

```c
#include<iostream>
#include<algorithm>
using namespace std;
int cmp(int a,int b)
{
    return a<b;
}
int main()
{
    int n;
    while(cin>>n)
    {
        int a[205];
        for(int i=0;i<n;i++)
        {
            cin>>a[i];
        }
        sort(a,a+n,cmp);
        cout<<a[0];
        for(int i=1;i<n;i++)
        {
            cout<<' '<<a[i];
        }
        cout<<endl;
    }
    return 0;
}
```