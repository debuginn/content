---
title: "蓝桥杯 基础练习 01字串"
date: 2019-01-06T19:15:49+08:00
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

对于长度为5位的一个01串，每一位都可能是0或1，一共有32种可能。它们的前几个是：

```shell
00000
00001
00010
00011
00100
```

请按从小到大的顺序输出这32种01串。

## 输入格式

本试题没有输入。

## 输出格式

输出32行，按从小到大的顺序每行一个长度为5的01串。

## 样例输出

```shell
00000
00001
00010
00011
<以下部分省略>
```

## C++代码

```c
#include <iostream>
using namespace std;
int main()
{
    for (int i = 0; i <= 1; ++i)
        for (int j = 0; j <= 1; ++j)
            for (int k = 0; k <= 1; ++k)
                for (int l = 0; l <= 1; ++l)
                    for (int m = 0; m <= 1; ++m)
                        cout << i << j << k << l << m << endl;
    return 0;
}
```