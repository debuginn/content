---
title: "数据结构 折半查找法"
date: 2017-11-18T14:26:16+08:00
keywords: "笔记,数据结构"
comments: true
tags: ["笔记", "数据结构"]
categories: ["algorithm"]
image: "https://webp.debuginn.com/202302221903175.jpg"
---

## 算法原理

先确定待查记录所在的范围（区间），然后逐步缩小范围指导找到或找不到该记录为止。

## 算法性能

时间复杂度： log 2 n + 1
平均查找长度： log 2 n + 1 – 1

## 注意事项

- 折半查找法必须为有序数列。
- 可以是逆序的，但是必须得提前定义遍历对比对象。

## 算法实现

```c
include "stdio.h"
//折半查找函数 
int binarySearch(int a[], int n, int key){
  //定义数组的第一个数 
  int low = 0;
  //定义数组的最后一个数
  int high = n-1;
  //定义中间的数值
  int mid;
  //存放中间的数值的变量
  int midVal; 
  //当左边的值小于等于右边的值的时候 
  while(low <= high){
      mid    = (low + high)/2;
      midVal = a[mid];
      //如果中间值小于用户查找到的数值，最低的数字到中间数值+1位置上 
      if(midVal < key){
          low = mid + 1;
      }else if(midVal > key){
      //如果中间值大于用户查找到的数值，最高的数字到中间数值-1位置上 
          high = mid - 1;
      }
      else
          return mid;   
  }
  return -1;    
}
int main(){
  int i, val, ret;
  int a[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
  //打印数组
  for(i=0; i<10; i++)
      printf("%d \t", a[i]);
  printf("\n请你输入你要进行查找的元素\n");
  scanf("%d", &val);
  ret = binarySearch(a, 10, val);
  if(ret == -1){
      printf("查找失败！\n");
  } else{
      printf("查找成功！\n");
  }
  return 0;
} 
```