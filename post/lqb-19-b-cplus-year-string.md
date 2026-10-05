---
title: "蓝桥杯 2019第十届蓝桥杯B组C++ 年号字串"
date: 2019-03-27T21:47:41+08:00
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

小明用字母A 对应数字1，B 对应2，以此类推，用Z 对应26。

对于27 以上的数字，小明用两位或更长位的字符串来对应，例如AA 对应27，AB 对
应28，AZ 对应52，LQ 对应329。

请问2019 对应的字符串是什么？

```sql
#include <iostream>
using namespace std;
void solve(int n) {
	if (!n) {
		return ;
	}
	solve(n / 26);
	cout << (char)(n % 26 + 64);
}
int main() {
	solve(2019);
	return 0;
}
```
答案：BYQ