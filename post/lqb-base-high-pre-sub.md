---
title: "蓝桥杯 基础练习 高精度加法"
date: 2019-02-05T22:03:28+08:00
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

输入两个整数a和b，输出这两个整数的和。a和b都不超过100位。

## 算法描述

由于a和b都比较大，所以不能直接使用语言中的标准数据类型来存储。对于这种问题，一般使用数组来处理。

定义一个数组A，`A[0]`用于存储a的个位，`A[1]`用于存储a的十位，依此类推。同样可以用一个数组B来存储b。

计算`c=a+b`的时候，首先将`A[0]`与`B[0]`相加，如果有进位产生，则把进位（即和的十位数）存入r，把和的个位数存入`C[0]`，即`C[0]`等于`(A[0]+B[0])%10`。然后计算`A[1]`与`B[1]`相加，这时还应将低位进上来的值r也加起来，即`C[1]`应该是`A[1]`、`B[1]`和r三个数的和．如果又有进位产生，则仍可将新的进位存入到r中，和的个位存到`C[1]`中。依此类推，即可求出C的所有位。

最后将C输出即可。

## 输入格式

输入包括两行，第一行为一个非负整数a，第二行为一个非负整数b。两个整数都不超过100位，两数的最高位都不是0。

## 输出格式

输出一行，表示a+b的值。

## 样例输入

```shell
20100122201001221234567890
2010012220100122
```

## 样例输出

```shell
20100122203011233454668012
```

## C++算法

```c
#include <cstdio>
#include <iostream>
#include <cstring>
using namespace std;
int	a[401], alen, b[401], blen, c[400], clen;
char	st[400];
int main()
{
	int i, j, n, len;
	scanf( "%s", st );
	alen = strlen( st );
	for ( i = 1; i <= alen; i++ )
		a[i] = st[alen - i] - 48;
	scanf( "%s", st );
	blen = strlen( st );
	for ( i = 1; i <= blen; i++ )
		b[i] = st[blen - i] - 48;
	if ( alen > blen )
		clen = alen;
	else clen = blen;
	for ( i = 1; i <= clen; i++ )
		c[i] = a[i] + b[i];
	for ( i = 1; i <= clen; i++ )
	{
		if ( c[i] >= 10 )
		{
			c[i + 1]	= c[i + 1] + c[i] / 10;
			c[i]		= c[i] % 10;
		}
	}
	if ( c[clen + 1] > 0 )
		clen++;
	for ( i = clen; i >= 1; i-- )
	{
		printf( "%d", c[i] );
	}
	printf( "\n" );
	return(0);
}
```