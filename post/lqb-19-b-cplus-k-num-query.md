---
title: "蓝桥杯 算法训练 区间k大数查询"
date: 2019-03-04T22:27:19+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "蓝桥杯"
comments: true
tags: ["蓝桥杯","algorithm"]
categories: ["algorithm"]
image: "https://webp.debuginn.com/202303241303887.jpg"
---

## 问题描述

给定一个序列，每次询问序列中第l个数到第r个数中第K大的数是哪个。

## 输入格式

- 第一行包含一个数n，表示序列长度。
- 第二行包含n个正整数，表示给定的序列。
- 第三个包含一个正整数m，表示询问个数。 
- 接下来m行，每行三个数l,r,K，表示询问序列从左往右第l个数到第r个数中，从大往小第K大的数是哪个。序列元素从1开始标号。输出格式总共输出m行，每行一个数，表示询问的答案。

## 样例输入

```shell
5
1 2 3 4 5
2
1 5 2
2 3 2
```

## 样例输出

```shell
4
2
```

## 数据规模与约定

对于30%的数据，n,m<=100；

对于100%的数据，n,m<=1000；

保证k<=(r-l+1)，序列中的数<=106。

## C++算法解析

```c
#include "iostream"
#include "algorithm"
using namespace std;
int a[1001], b[1001];

bool cmp(int a, int b){
	return a>b;
} 
int main(){
	int n, m;
	int l, r, k;
	int i, j;
	
	while(cin>>n){
		for(i=0; i<n; i++){
			cin >> a[i];
		}
		
		cin >> m;
		while(m--){
			cin >>l>>r>>k;
			
			for(j=l-1, i=0; j<r; ++j,++i){
				b[i] = a[j];
			}
			
			sort(b, b+i, cmp);
			cout<<b[k-1]<<endl;
		}
	}
	return 0;
}
```