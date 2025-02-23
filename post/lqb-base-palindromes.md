---
title: "蓝桥杯 基础练习 回文数"
date: 2019-01-12T18:58:58+08:00
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

1221是一个非常特殊的数，它从左边读和从右边读是一样的，编程求所有这样的四位十进制数。

## 输出格式　　

按从小到大的顺序输出满足条件的四位十进制数。

## C++算法

```c
#include<stdio.h>
int main()
{
	for(int i1=1;i1<10;i1++)
	{
		for(int i2=0;i2<10;i2++)
		{
			for(int i3=0;i3<10;i3++)
			{
				for(int i4=0;i4<10;i4++)
				{
					if(i1==i4 && i2==i3)
					printf("%d%d%d%d\n",i1,i2,i3,i4);
				}
				
			}
		}
	}
	return 0;
}
```
