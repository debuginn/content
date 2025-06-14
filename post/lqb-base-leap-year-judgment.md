---
title: "蓝桥杯 基础练习 闰年判断"
date: 2019-01-05T19:17:57+08:00
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

给定一个年份，判断这一年是不是闰年。

当以下情况之一满足时，这一年是闰年：

- 年份是4的倍数而不是100的倍数；
- 年份是400的倍数。
- 其他的年份都不是闰年。

## 输入格式

输入包含一个整数y，表示当前的年份。

## 输出格式

输出一行，如果给定的年份是闰年，则输出yes，否则输出no。

> 说明：当试题指定你输出一个字符串作为结果（比如本题的yes或者no，你需要严格按照试题中给定的大小写，写错大小写将不得分。

样例输入2013

样例输出no

样例输入2016

样例输出yes

## 数据规模与约定

1990 <= y <= 2050。

## C++源代码

```c
#include <iostream>
using namespace std;
int main()
{
    int y;
    cin >> y;
    if (y%4==0 && y%100!=0 || y%400==0)
        cout << "yes" << endl;
    else
        cout << "no" << endl;
    return 0;
}
```

