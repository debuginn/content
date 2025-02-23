---
title: "蓝桥杯 基础练习 2n皇后问题"
date: 2019-02-03T22:13:21+08:00
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

给定一个n*n的棋盘，棋盘中有一些位置不能放皇后。现在要向棋盘中放入n个黑皇后和n个白皇后，使任意的两个黑皇后都不在同一行、同一列或同一条对角线上，任意的两个白皇后都不在同一行、同一列或同一条对角线上。问总共有多少种放法？n小于等于8。

## 输入格式

输入的第一行为一个整数n，表示棋盘的大小。

接下来n行，每行n个0或1的整数，如果一个整数为1，表示对应的位置可以放皇后，如果一个整数为0，表示对应的位置不可以放皇后。

## 输出格式

输出一个整数，表示总共有多少种放法。

### 样例输入

```shell
4
1 1 1 1
1 1 1 1
1 1 1 1
1 1 1 1
```

### 样例输出

2

### 样例输入

```shell
4
1 0 1 1
1 1 1 1
1 1 1 1
1 1 1 1
```

### 样例输出

0

## C++算法

```c
#include<cstdio>
using namespace std;

int n;
int sum;
bool g[9][9];
bool wh[9];
bool wd[17];
bool wu[17];
bool bh[9];
bool bd[17];
bool bu[17];

void white(int h){
	if(h==n){
		sum++;
	}else{
		for(int i=0;i<n;i++){
			if(!g[h][i])continue;
			if(wh[i])continue;
			if(wd[i+h])continue;
			if(wu[(i-h)+n])continue;
			wh[i]=wd[i+h]=wu[(i-h)+n]=1;
			white(h+1);			
			wh[i]=wd[i+h]=wu[(i-h)+n]=0;
		}
	}
}

void black(int h){
	if(h==n){
		white(0);
	}else{
		for(int i=0;i<n;i++){
			if(!g[h][i])continue;
			if(bh[i])continue;
			if(bd[i+h])continue;
			if(bu[(i-h)+n])continue;
			g[h][i]=0;
			bh[i]=bd[i+h]=bu[(i-h)+n]=1;
			black(h+1);			
			g[h][i]=1;
			bh[i]=bd[i+h]=bu[(i-h)+n]=0;
		}
	}
}

int main(){
	int i;
	int x;
	sum=0;
	scanf("%d",&n);
	for(i=0;i<n;i++){
		wh[i]=bh[i]=0;
		wd[i]=bd[i]=0;
		wu[i]=bu[i]=0;
		for(int j=0;j<n;j++){
			scanf("%d",&x);
			g[i][j]=(bool)x;
		}
	}
	for(;i<2*n;i++){
		wd[i]=bd[i]=0;
		wu[i]=bu[i]=0;
	}
	black(0);
	printf("%d\n",sum);
	return 0;
}
```
