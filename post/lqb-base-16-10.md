---
title: "蓝桥杯 基础练习 十六进制转十进制"
date: 2019-01-15T21:47:43+08:00
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

从键盘输入一个不超过8位的正的十六进制数字符串，将它转换为正的十进制数后输出。

> 注：十六进制数中的10~15分别用大写的英文字母A、B、C、D、E、F表示。

## 样例输入

FFFF

## 样例输出

65535

## C++算法

```c
#include<iostream>
#include<string>
using namespace std;
int main()
{
    string s;
    while(cin>>s)
    {
        int leth=s.length();
        long long sum=0;
        for(int i=0;i<leth;i++)
        {
            if(s[i]>='A'&&s[i]<='F')
            {
                sum=sum*16+s[i]-'A'+10;
               // cout<<sum<<endl;
            }
            else
            {
                sum=sum*16+s[i]-'0';
                //cout<<sum<<endl;
            }
        }
        cout<<sum<<endl;
    }
    return 0;
}
```


