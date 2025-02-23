---
title: "蓝桥杯 基础练习 报时助手"
date: 2019-02-02T22:16:23+08:00
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

给定当前的时间，请用英文的读法将它读出来。

时间用时h和分m表示，在英文的读法中，读一个时间的方法是：

- 如果m为0，则将时读出来，然后加上“o'clock”，如3:00读作“three o'clock”。 
- 如果m不为0，则将时读出来，然后将分读出来，如5:30读作“five thirty”。 
- 时和分的读法使用的是英文数字的读法，其中0~20读作： 
- 0:zero, 1: one, 2:two, 3:three, 4:four, 5:five, 6:six, 7:seven, 8:eight, 9:nine, 10:ten, 11:eleven, 12:twelve, 13:thirteen, 14:fourteen, 15:fifteen, 16:sixteen, 17:seventeen, 18:eighteen, 19:nineteen, 20:twenty。 
- 30读作thirty，40读作forty，50读作fifty。 
- 对于大于20小于60的数字，首先读整十的数，然后再加上个位数。如31首先读30再加1的读法，读作“thirty one”。 
- 按上面的规则21:54读作“twenty one fifty four”，9:07读作“nine seven”，0:15读作“zero fifteen”。

## 输入格式

输入包含两个非负整数h和m，表示时间的时和分。非零的数字前没有前导0。h小于24，m小于60。

## 输出格式

输出时间时刻的英文。

## 样例输入

0 15

## 样例输出

zero fifteen

## C++算法

```c
#include <iostream>
#include <string>
#include <map>
using namespace std;
int main(int argc, char** argv)
{
	map<int,string> maptime;
	maptime[0]="zero";
	maptime[1]="one";
	maptime[2]="two";
	maptime[3]="three";
	maptime[4]="four";
	maptime[5]="five";
	maptime[6]="six";
	maptime[7]="seven";
	maptime[8]="eight";
	maptime[9]="nine";
	maptime[10]="ten";
	maptime[11]="eleven";
	maptime[12]="twelve";
	maptime[13]="thirteen";
	maptime[14]="fourteen";
	maptime[15]="fifteen";
	maptime[16]="sixteen";
	maptime[17]="seventeen";
	maptime[18]="eighteen";
	maptime[19]="nineteen";
	maptime[20]="twenty";
	maptime[30]="thirty";
	maptime[40]="forty";
	maptime[50]="fifty";
	int h,m;
	cin>>h>>m;
	if(m==0)
	{
		if(h<=20)
		{
			cout<<maptime[h]<<" o'clock";
		}
		else
		{
			cout<<maptime[20]<<" "<<maptime[h-20]<<" o'clock";
		}
	}
	else
	{
		if(h<=20)
		{
			cout<<maptime[h]<<" ";
		}
		else
		{
			cout<<maptime[20]<<" "<<maptime[h-20]<<" ";
		}
		if(m<=20)
		{
			cout<<maptime[m]<<" ";
		}
		else
		{
			int k=m%10;
			cout<<maptime[m-k]<<" "<<maptime[k]<<" ";
		}
	}
	return 0;
}
```