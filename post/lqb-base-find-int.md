---
title: "蓝桥杯 基础练习 查找整数"
date: 2019-01-09T19:05:04+08:00
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

给出一个包含n个整数的数列，问整数a在数列中的第一次出现是第几个。

## 输入格式

第一行包含一个整数n。

第二行包含n个非负整数，为给定的数列，数列中的每个数都不大于10000。

第三行包含一个整数a，为待查找的数。

## 输出格式

如果a在数列中出现了，输出它第一次出现的位置(位置从1开始编号)，否则输出-1。

## 样例输入

6
1 9 4 8 3 9
9

## 样例输出
2

## 数据规模与约定

1 <= n <= 1000。

## C++代码

```c
#include <iostream>
using namespace std;
const int MAXN = 10001;
int n, a, ans;
int s[MAXN];
int main()
{
    cin >> n;
    for (int i = 0; i < n; ++i)
        cin >> s[i];
    cin >> a;
    ans = -1;
    for (int i = 0; i < n; ++i)
    {
        if (s[i] == a)
        {
            ans = i + 1;
            break;
        }
    }
    cout << ans << endl;
    return 0;
}
```
