---
title: "数据结构 分块查找法"
date: 2017-11-27T21:58:31+08:00
keywords: "笔记,数据结构,算法"
comments: true
tags: ["笔记", "数据结构"]
categories: ["algorithm"]
image: "https://webp.debuginn.com/202302221903175.jpg"
---

## 算法定义

分块查找，也叫索引顺序查找，算法实现除了需要查找表本身之外，还需要根据查找表建立一个索引表。

> 建立的索引表要求按照关键字进行升序排序，查找表要么整体有序，要么分块有序。

**分块有序**：指的是第二个子表中所有关键字都要大于第一个子表中的最大关键字，第三个子表的所有关键字都要大于第二个子表中的最大关键字，依次类推。

块（子表）中各关键字的具体顺序，根据各自可能会被查找到的概率而定。如果各关键字被查找到的概率是相等的，那么可以随机存放；否则可按照被查找概率进行降序排序，以提高算法运行效率。

## 算法原理

所有前期准备工作完成后，开始在此基础上进行分块查找。分块查找的过程分为两步进行：

确定要查找的关键字可能存在的具体块（子表）；

在具体的块中进行顺序查找。

## 方法描述

将n个数据元素”按块有序”划分为m块（m ≤ n）。每一块中的结点不必有序，但块与块之间必须”按块有序”；即第1块中任一元素的关键字都必须小于第2块中任一元素的关键字；而第2块中任一元素又都必须小于第3块中的任一元素，……。

## 算法实现

```c
#include <stdio.h>
#include <stdlib.h>
struct index {  //定义块的结构
    int key;
    int start;
} newIndex[3];   //定义结构体数组
int search(int key, int a[]){
    int i, startValue;
    i = 0;
    while (i<3 && key>newIndex[i].key) { //确定在哪个块中，遍历每个块，确定key在哪个块中
        i++;
    }
    if (i>=3) {  //大于分得的块数，则返回0
        return -1;
    }
    startValue = newIndex[i].start;  //startValue等于块范围的起始值
    while (startValue <= startValue+5 && a[startValue]!=key)
    {
        startValue++;
    }
    if (startValue>startValue+5) {  //如果大于块范围的结束值，则说明没有要查找的数
        return -1;
    }
    return startValue;
}
int cmp(const void *a,const void* b){
    return (*(struct index*)a).key>(*(struct index*)b).key?1:-1;
}
int main(){
    int i, j=-1, k, key;
    int a[] = {33,42,44,38,24,48, 22,12,13,8,9,20,  60,58,74,49,86,53};
    //确认模块的起始值和最大值
    for (i=0; i<3; i++) {
        newIndex[i].start = j+1;  //确定每个块范围的起始值
        j += 6;
        for (int k=newIndex[i].start; k<=j; k++) {
            if (newIndex[i].key<a[k]) {
                newIndex[i].key=a[k];
            }
        }
    }
    //对结构体按照 key 值进行排序
    qsort(newIndex,3, sizeof(newIndex[0]), cmp);
    //输入要查询的数，并调用函数进行查找
    printf("请输入您想要查找的数：\n");
    scanf("%d", &key);
    k = search(key, a);
    //输出查找的结果
    if (k>0) {
        printf("查找成功！您要找的数在数组中的位置是：%d\n",k+1);
    }else{
        printf("查找失败！您要找的数不在数组中。\n");
    }
    return 0;
}
```