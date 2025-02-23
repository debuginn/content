---
title: "蓝桥杯 基础练习 数列特征"
date: 2019-01-08T19:10:50+08:00
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

给出n个数，找出这n个数的最大值，最小值，和。

## 输入格式

第一行为整数n，表示数的个数。

第二行有n个数，为给定的n个数，每个数的绝对值都小于10000。

## 输出格式

输出三行，每行一个整数。第一行表示这些数中的最大值，第二行表示这些数中的最小值，第三行表示这些数的和。

## 样例输入

5
1 3 -2 4 5

## 样例输出

5
-2
11

## 数据规模与约定

1 <= n <= 10000。

## C++代码

```c
#include<cstdio>
#include<iostream>
#include<algorithm>
#include<cstring>
using namespace std;
int main(){
    int n;
    while(cin>>n){
        int a[10005];
        int sum=0;
        for(int i=0;i<n;i++){
            scanf("%d",&a[i]);
            sum+=a[i];
        }
        sort(a,a+n);
        cout<<a[n-1]<<endl<<a[0]<<endl<<sum<<endl;
    }
    return 0;
}
```