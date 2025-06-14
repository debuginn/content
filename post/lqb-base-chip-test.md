---
title: "蓝桥杯 基础练习 芯片测试"
date: 2019-01-30T22:38:26+08:00
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

有`n（2≤n≤20）`块芯片，有好有坏，已知好芯片比坏芯片多。

每个芯片都能用来测试其他芯片。用好芯片测试其他芯片时，能正确给出被测试芯片是好还是坏。而用坏芯片测试其他芯片时，会随机给出好或是坏的测试结果（即此结果与被测试芯片实际的好坏无关）。

给出所有芯片的测试结果，问哪些芯片是好芯片。

## 输入格式

输入数据第一行为一个整数n，表示芯片个数。 

第二行到第n+1行为n*n的一张表，每行n个数据。表中的每个数据为0或1，在这n行中的第i行第j列（1≤i, j≤n）的数据表示用第i块芯片测试第j块芯片时得到的测试结果，1表示好，0表示坏，i=j时一律为1（并不表示该芯片对本身的测试结果。芯片不能对本身进行测试）。

## 输出格式

按从小到大的顺序输出所有好芯片的编号

## 样例输入

```shell
3
1 0 1
0 1 0
1 0 1
```

## 样例输出

1 3

## C++算法

```c
#include<iostream> 
#include<cstdio> 
#include<cstring> 

using namespace std; 

bool a[25][25]; 
bool v[25]; 
int n; 

bool dfs(int k) 
{ 
    if (k==n) 
    { 
        int sum=0; 
        for (int i=1;i<=n;i++) 
           if (v[i]) 
             sum++; 
         if (sum>n-sum) 
           for (int i=1;i<=n;i++) 
              if (v[i]) 
                 printf("%d ",i); 
        return true; 
    } 
     
    if (v[k]==true) 
    { 
       int len=0,s[25]; 
       for (int i=1;i<=n;i++) 
            if (!a[k][i] && v[i]) 
            { 
               s[++len]=i; 
               v[i]=false; 
            } 
       if (dfs(k+1)) return true; 
       for (int i=1;i<=len;i++) 
          v[s[i]]=true; 
    } 
     
    if (dfs(k+1)) return true; 
      
} 
int main() 
{ 
     scanf("%d",&n); 
     memset(v,true,sizeof(v)); 
     for (int i=1;i<=n;i++) 
        for (int j=1;j<=n;j++) 
        { 
            int c; 
            scanf("%d",&c); 
            if (c) a[i][j]=1; else a[i][j]=0; 
        } 
  
     dfs(1); 
     return 0; 
}
```