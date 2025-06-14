---
title: "蓝桥杯 基础练习 字符串对比"
date: 2019-01-19T21:39:25+08:00
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

给定两个仅由大写字母或小写字母组成的字符串(长度介于1到10之间)，它们之间的关系是以下4中情况之一： 
1. 两个字符串长度不等。比如 Beijing 和 Hebei 
2. 两个字符串不仅长度相等，而且相应位置上的字符完全一致(区分大小写)，比如 Beijing 和 Beijing 
3. 两个字符串长度相等，相应位置上的字符仅在不区分大小写的前提下才能达到完全一致（也就是说，它并不满足情况2）。比如 beijing 和 BEIjing 
4. 两个字符串长度相等，但是即使是不区分大小写也不能使这两个字符串一致。比如 Beijing 和 Nanjing
5. 编程判断输入的两个字符串之间的关系属于这四类中的哪一类，给出所属的类的编号。

## 输入格式　　

包括两行，每行都是一个字符串

## 输出格式

仅有一个数字，表明这两个字符串的关系编号

## 样例输入

BEIjing
beiJing

## 样例输出

3

## C++算法

```c
#include<iostream>
#include<cstdio>
#include<cstring>
#include<cmath>
using namespace std;

char A[15],B[15];

int main()
{
    scanf("%s",A);
    scanf("%s",B);
    int a=strlen(A);
    int b=strlen(B);
    int count=0;
    if(a!=b) //长度不等
    {
        printf("1\n");
        return 0;
    }
    //长度相等

    else
    {
        for(int i=0;i<a;i++)
        {
            if((A[i]!=B[i]))
            {
                if(abs(A[i]-B[i])!=32)
                {
                    printf("4\n");
                    return 0;
                }
                else 
                {
                    ++count;
                    continue;
                }
            }
        }
        if((count==0))
            printf("2\n");
        else
            printf("3\n");
    }
    return 0;
}
```