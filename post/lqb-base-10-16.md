---
title: "蓝桥杯 基础练习 十进制转十六进制"
date: 2019-01-14T21:49:07+08:00
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

十六进制数是在程序设计时经常要使用到的一种整数的表示方式。它有0,1,2,3,4,5,6,7,8,9,A,B,C,D,E,F共16个符号，分别表示十进制数的0至15。十六进制的计数方法是满16进1，所以十进制数16在十六进制中是10，而十进制的17在十六进制中是11，以此类推，十进制的30在十六进制中是1E。
　　给出一个非负整数，将它表示成十六进制的形式。

## 输入格式　　

输入包含一个非负整数a，表示要转换的数。0<=a<=2147483647

## 输出格式　　

输出这个整数的16进制表示

样例输入30

样例输出1E

## C++算法

```c
#include<iostream>
#include<cstdio>
using namespace std;
int main()
{
    int n;
    char s[100000];
    while(cin>>n)
    {
        int k=0;
        if(n==0)
        {
            cout<<0;
        }
        else
        {
           while(n!=0)
           {
               if(n%16>=10)
               {
                   s[k++]='A'+n%16-10;
               }
               else
               {
                   s[k++]='0'+n%16;
               }
               n=n/16;
           }
           for(int i=k-1;i>=0;i--)
           {
               cout<<s[i];
           }
        }
       cout<<endl;
    }
    return 0;
}
```

