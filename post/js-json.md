---
title: "JavaScript 对象表示法JSON"
date: 2018-07-16T19:12:22+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "json"
comments: true
weight: 0
tags: [ "js","json" ]
categories: [ "js" ]
image: "https://static.debuginn.com/202304131856440.jpg"
---

## JSON基本概念

- JSON：JavaScript对象表示法（JavaScript Object Notation） 
- JSON是存储和交换文本信息的语法，类似XML。采用键值对的方式来组织，易于人们阅读和编写，同时也有益于机器解析与生成。 
- JSON是独立于语言的，不管是什么语言，都可以及逆行解析json，按照json规则来就行。

## JSON和XML对比

- JSON的长度相对于XML来说比较短小
- JSON读写速度比较快
- JSON可以使用JavaScript内建的方法直接进行解析，转换成JavaScript对象，十分的方便

## 语法规则

书写格式是：名称/值对

名称/值对组合中的名称写在前面（在双引号中），值对写在后面（同样在双引号中），中间用冒号隔开，比如“name”:”张三”