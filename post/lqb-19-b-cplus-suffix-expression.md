---
title: "蓝桥杯 2019第十届蓝桥杯B组C++ 后缀表达式"
date: 2019-04-01T22:02:39+08:00
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

给定N 个加号、M 个减号以及N + M + 1 个整数A1; A2; ......; AN+M+1，小 明想知道在所有由这N 个加号、M 个减号以及N + M +1 个整数凑出的合法的 后缀表达式中，结果最大的是哪一个？请你输出这个最大的结果。
例如使用1 2 3 + -，则“2 3 + 1 -” 这个后缀表达式结果是4，是最大的。

## 输入格式

第一行包含两个整数N 和M。
第二行包含N + M + 1 个整数A1; A2; ...... ; AN+M+1。

## 输出格式

输出一个整数，代表答案。

## 样例输入

```c
1 1
1 2 3
```

## 样例输出

```c
4
```

## 评测用例规模与约定

对于所有评测用例，0 <= N; M >= 100000，109 >= Ai <= 109。