---
title: "蓝桥杯 2017年省赛C++B组题1 购物单"
date: 2019-03-18T22:00:16+08:00
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

小明刚刚找到工作，老板人很好，只是老板夫人很爱购物。老板忙的时候经常让小明帮忙到商场代为购物。小明很厌烦，但又不好推辞。

这不，XX大促销又来了！老板夫人开出了长长的购物单，都是有打折优惠的。

小明也有个怪癖，不到万不得已，从不刷卡，直接现金搞定。

现在小明很心烦，请你帮他计算一下，需要从取款机上取多少现金，才能搞定这次购物。

取款机只能提供100元面额的纸币。小明想尽可能少取些现金，够用就行了。

你的任务是计算出，小明最少需要取多少现金。

以下是让人头疼的购物单，为了保护隐私，物品名称被隐藏了。

```shell
-----------------
****     180.90       88折
****      10.25       65折
****      56.14        9折
****     104.65        9折
****     100.30       88折
****     297.15        半价
****      26.75       65折
****     130.62        半价
****     240.28       58折
****     270.62        8折
****     115.87       88折
****     247.34       95折
****      73.21        9折
****     101.00        半价
****      79.54        半价
****     278.44        7折
****     199.26        半价
****      12.97        9折
****     166.30       78折
****     125.50       58折
****      84.98        9折
****     113.35       68折
****     166.57        半价
****      42.56        9折
****      81.90       95折
****     131.78        8折
****     255.89       78折
****     109.17        9折
****     146.69       68折
****     139.33       65折
****     141.16       78折
****     154.74        8折
****      59.42        8折
****      85.44       68折
****     293.70       88折
****     261.79       65折
****      11.30       88折
****     268.27       58折
****     128.29       88折
****     251.03        8折
****     208.39       75折
****     128.88       75折
****      62.06        9折
****     225.87       75折
****      12.89       75折
****      34.28       75折
****      62.16       58折
****     129.12        半价
****     218.37        半价
****     289.69        8折
--------------------
```

需要说明的是，88折指的是按标价的88%计算，而8折是按80%计算，余者类推。
特别地，半价是按50%计算。

请提交小明要从取款机上提取的金额，单位是元。
答案是一个整数，类似4300的样子，结尾必然是00，不要填写任何多余的内容。

## 解题思路

其实这题就是送分的，不过就是送分了也是很容易丢分的，一不小心就少算了一个，分就没了，所以还是需要谨慎的。这个题目其实有好多的解法。

### 解题思路一

笨办法，就是将数值一个一个的输入并且用for循环进行sum求和运算，最后输出结果，不过不推荐，比较麻烦。

### 解题思路二

相信在考试的机器中都存在Office这个神奇的软件，那么恭喜你，有了Excel这个软件，哈哈，将数值复制进去，拆分单元格，之后sum函数求和，最后得到你的结果，试试吧！

## 解题答案

5200