---
title: "蓝桥杯 算法训练 最大最小公倍数"
date: 2019-03-05T22:23:41+08:00
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

已知一个正整数N，问从1~N中任选出三个数，他们的最小公倍数最大可以为多少。

## 输入格式

输入一个正整数N。

## 输出格式

输出一个整数，表示你找到的最小公倍数。

## 样例输入

9

## 样例输出

504

## 数据规模与约定

1 <= N <= 106。

## 算法分析

1. 如果 n <= 2, 那么最小公倍数为 n 
2. 如果 n 是奇数，那么最小公倍数的最大值为末尾的三个数相乘 
3. 如果是偶数的话，如果同时出现两个偶数肯定会不能构成最大值了，因为会被除以2分两种情况： 
   - 如果 n 是偶数且不是三的倍数， 比如8，那么跳过n-2这个数而选择 8 7 5 能保证不会最小公倍数被除以2所以最小公倍数的最大值为n * (n – 1) * (n – 3)
   - 如果 n 是偶数且为三的倍数，比如6，如果还像上面那样选择的话，6和3相差3会被约去一个3，又不能构成最大值了。那么最小公倍数的最大值为(n – 1) * (n – 2) * (n – 3)

## C++算法

```c
#include "iostream" 
#include "algorithm"
using namespace std;
int main(){
	long long n, ans;
	cin >> n;
	if(n <= 2){
		ans = n;
	}else if(n%2 == 1){
		ans = n * (n-1) * (n-2);
	}else if(n%3 == 0){
		ans = (n-1) * (n-2) * (n-3);
	}else{
		ans = n * (n-1) * (n-3);
	}
	cout << ans;
	return 0;
}
```