---
title: "蓝桥杯 2017年省赛C++B组题5 取数位"
date: 2019-03-19T21:57:56+08:00
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

求1个整数的第k位数字有很多种方法。

以下的方法就是一种。

```c
// 求x用10进制表示时的数位长度 
int len(int x){
	if(x<10) return 1;
	return len(x/10)+1;
}
	
// 取x的第k位数字
int f(int x, int k){
	if(len(x)-k==0) return x%10;
	return _____________________;  //填空
}
	
int main()
{
	int x = 23574;
	printf("%d\n", f(x,3));
	return 0;
}
```

对于题目中的测试数据，应该打印5。

请仔细分析源码，并补充划线部分所缺少的代码。

注意：只提交缺失的代码，不要填写任何已有内容或说明性的文字。

## 解题算法

```c
#include "iostream"
#include "algorithm"
 
// 求x用10进制表示时的数位长度 
int len(int x){
	if(x<10) return 1;
	return len(x/10)+1;
}
	
// 取x的第k位数字
int f(int x, int k){
	if(len(x)-k==0) return x%10;
	return f(x/10, k);  //填空
}
	
int main()
{
	int x = 23574;
	printf("%d\n", f(x,3));
	return 0;
}
```

## 解题答案

f(x/10, k)