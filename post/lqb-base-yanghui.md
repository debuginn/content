---
title: "蓝桥杯 基础练习 杨辉三角形"
date: 2019-01-10T19:02:21+08:00
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

杨辉三角形又称Pascal三角形，它的第i+1行是(a+b)i的展开式的系数。

它的一个重要性质是：三角形中的每个数字等于它两肩上的数字相加。

下面给出了杨辉三角形的前4行：

```shell
   1
  1 1
 1 2 1
1 3 3 1
```

给出n，输出它的前n行。

## 输入格式

输入包含一个数n。

## 输出格式

输出杨辉三角形的前n行。每一行从这一行的第一个数开始依次输出，中间使用一个空格分隔。请不要在前面输出多余的空格。

## 样例输入

4

## 样例输出

```shell
1
1 1
1 2 1
1 3 3 1
```

## 数据规模与约定

1 <= n <= 34。

## C++代码

```c
#include <iostream>
using namespace std;
const int MAXN = 40;
int n;
int a[MAXN][MAXN];
int main()
{
    cin >> n;
    a[0][0] = 1;
    for (int i = 0; i < n; ++i)
    {
        a[i][0] = a[i][i] = 1;
        for (int j = 1; j < i; ++j)
            a[i][j] = a[i-1][j-1] + a[i-1][j];
    }
    for (int i = 0; i < n; ++i)
    {
        for (int j = 0; j <= i; ++j)
            cout << a[i][j] << " ";
        cout << endl;
    }
    return 0;
}
```