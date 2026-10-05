---
title: "蓝桥杯 2017年省赛C++B组题2 等差素数列"
date: 2019-03-19T21:52:37+08:00
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: ["蓝桥杯"]
comments: true
weight: 0
tags: ["蓝桥杯","algorithm"]
categories: ["algorithm"]
image: "https://static.debuginn.com/202303241303887.jpg"
---

2,3,5,7,11,13,….是素数序列。

类似：7,37,67,97,127,157 这样完全由素数组成的等差数列，叫等差素数数列。

上边的数列公差为30，长度为6。

2004年，格林与华人陶哲轩合作证明了：存在任意长度的素数等差数列。
这是数论领域一项惊人的成果！

有这一理论为基础，请你借助手中的计算机，满怀信心地搜索：

长度为10的等差素数列，其公差最小值是多少？

注意：需要提交的是一个整数，不要填写任何多余的内容和说明文字。

## 解题算法

```c
#include "iostream"
#include "algorithm"

using namespace std;
typedef long long ll;

bool isprime(int n){
	//如果数值小于等于1并且大于二且为偶数 
	if(n<=1 || (n>2 && n%2==0)){
		return false;		
	}
	//查找最小公倍数对应的偶数序列，是否满足条件 
	for(ll i=3; i*i<=n; i+=2){
		if(n%i==0){
			return false;
		}
	}	
	return true;
}

int main(){
	for(int d = 2; d<1000; d++){
		for(ll  n = 2; n<1000; ++n){
			if(
				isprime(n)
				&& isprime(n + d)
				&& isprime(n + 2*d) 
				&& isprime(n + 3*d)
				&& isprime(n + 4*d)
				&& isprime(n + 5*d)
				&& isprime(n + 6*d)
				&& isprime(n + 7*d)
				&& isprime(n + 8*d)
				&& isprime(n + 9*d)
			){
				cout << d <<endl;
				break;
			}
		}
	}
	return 0;
}
```

## 题解答案

210
