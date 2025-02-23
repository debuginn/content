---
title: "蓝桥杯 基础练习 数的读法"
date: 2019-01-29T22:45:32+08:00
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

Tom教授正在给研究生讲授一门关于基因的课程，有一件事情让他颇为头疼：一条染色体上有成千上万个碱基对，它们从0开始编号，到几百万，几千万，甚至上亿。
　　
比如说，在对学生讲解第1234567009号位置上的碱基时，光看着数字是很难准确的念出来的。
　　
所以，他迫切地需要一个系统，然后当他输入12 3456 7009时，会给出相应的念法：
　　
十二亿三千四百五十六万七千零九
　　
用汉语拼音表示为
　　
shi er yi san qian si bai wu shi liu wan qi qian ling jiu
　　
这样他只需要照着念就可以了。
　　
你的任务是帮他设计这样一个系统：给定一个阿拉伯数字串，你帮他按照中文读写的规范转为汉语拼音字串，相邻的两个音节用一个空格符格开。
　　
注意必须严格按照规范，比如说“10010”读作“yi wan ling yi shi”而不是“yi wan ling shi”，“100000”读作“shi wan”而不是“yi shi wan”，“2000”读作“er qian”而不是“liang qian”。

## 输入格式

有一个数字串，数值大小不超过2,000,000,000。

## 输出格式

是一个由小写英文字母，逗号和空格组成的字符串，表示该数的英文读法。

## 样例输入

1234567009

## 样例输出

shi er yi san qian si bai wu shi liu wan qi qian ling jiu

## C++算法

```c
#include<iostream>
#include<cstdio>
#include<cstring>
#include<cstdlib>

using namespace std;
char df[][10]={"ling","yi","er","san","si","wu","liu","qi","ba","jiu"};
char s[15];
int main()
 {
 scanf("%s",s);
 int lens=strlen(s);
 bool bk=false;
 for (int i=0;i<lens;i++)
 {
     int p,lendf;
     p=s[i]-'0';
     if (p!=0)
     {
         bk=false;
         lendf=strlen(df[p]);

         if (s[i-1]-'0'==0) 
         printf("ling ");

         if ((lens-i)%4==2 && p==1 /*&& s[i-1]-'0'==0 && s[i-2]-'0'==0*/ && i==0)
         {
             printf("shi ");
             continue;
         }

     for (int j=0;j<lendf;j++)
         printf("%c",df[p][j]);
     printf(" ");

     if ((lens-i)%4==2) printf("shi ");
     if ((lens-i)%4==3) printf("bai ");
     if ((lens-i)%4==0) printf("qian ");
 }
 if ((lens-i)%4==1)
 {
     if ((lens-i)/4==2)
     {
         bk=true;
         printf("yi ");
     }
     if (bk==false && (lens-i)/4==1) 
        printf("wan ");
     }
 }
     return 0;
 } 
```