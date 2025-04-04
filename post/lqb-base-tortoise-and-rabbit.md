---
title: "蓝桥杯 基础练习 龟兔赛跑预测"
date: 2019-01-31T22:35:27+08:00
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

话说这个世界上有各种各样的兔子和乌龟，但是研究发现，所有的兔子和乌龟都有一个共同的特点——喜欢赛跑。于是世界上各个角落都不断在发生着乌龟和兔子的比赛，小华对此很感兴趣，于是决定研究不同兔子和乌龟的赛跑。他发现，兔子虽然跑比乌龟快，但它们有众所周知的毛病——骄傲且懒惰，于是在与乌龟的比赛中，一旦任一秒结束后兔子发现自己领先t米或以上，它们就会停下来休息s秒。对于不同的兔子，t，s的数值是不同的，但是所有的乌龟却是一致——它们不到终点决不停止。
 　　然而有些比赛相当漫长，全程观看会耗费大量时间，而小华发现只要在每场比赛开始后记录下兔子和乌龟的数据——兔子的速度v1（表示每秒兔子能跑v1米），乌龟的速度v2，以及兔子对应的t，s值，以及赛道的长度l——就能预测出比赛的结果。但是小华很懒，不想通过手工计算推测出比赛的结果，于是他找到了你——清华大学计算机系的高才生——请求帮助，请你写一个程序，对于输入的一场比赛的数据v1，v2，t，s，l，预测该场比赛的结果。

## 输入格式

输入只有一行，包含用空格隔开的五个正整数`v1，v2，t，s，l`，其中`(v1,v2<=100;t<=300;s<=10;l<=10000且为v1,v2的公倍数)`

## 输出格式

输出包含两行，第一行输出比赛结果——一个大写字母“T”或“R”或“D”，分别表示乌龟获胜，兔子获胜，或者两者同时到达终点。

第二行输出一个正整数，表示获胜者（或者双方同时）到达终点所耗费的时间（秒数）。

## 样例输入

10 5 5 2 20

## 样例输出

```shell
D
4
```

## 样例输入

10 5 5 1 20

## 样例输出

```shell
R
3
```

## 样例输入

10 5 5 3 20

## 样例输出

```shell
T
4
```

## C++算法

```c
#include<cstdio>
 #include<iostream>
 using namespace std;
 int main()
 {
     int v1,v2,t,s,L,L1=0,L2=0,ans=0,i,j;
     bool bk=true;
     scanf("%d%d%d%d%d",&v1,&v2,&t,&s,&L);
     while(1)
     {
         if(L1-L2>=t&&L1<L&&L2<L)
         {
            for(i=1;i<=s;i++)
            {
               if(L1<L&&L2<L)
               {
              L2+=v2;
              ans++;
               }
            }
         }
         else
         {
             L2+=v2;
             L1+=v1;
             ans++;
         }
         if(L1>=L) break;
         if(L2>=L) break;
     }
     if(L1>=L&&L2>=L){printf("D\n%d\n",ans);return 0;}
     if(L1>=L) {printf("R\n%d\n",ans); return 0;}
     if(L2>=L) {printf("T\n%d\n",ans); return 0;}
     return 0;
 }

```