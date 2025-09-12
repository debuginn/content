---
title: "HTTP 中 Get 与 POST 请求的区别"
date: 2018-07-20T18:59:22+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "http,post,get"
comments: true
weight: 0
tags: ["http","post","get"]
categories: ["agreement"]
image: "https://webp.debuginn.com/202304131900179.jpg"
---

## 给服务器传递数据量

get方式的大小是受限于浏览器的，大部分浏览器是2k的限制；

每一个浏览器的限制是不一样的 Chrome的限制是8K

- http://网址/index.php?name=tom
- 上述请求get方式传递了9个字节的信息；
- 1024字节 = 1k

post原则没有限制，php.ini最其限制为8M

## 安全方面

POST传输数据相对来说比较安全。

## 传输数据的形式不一样

- Get方式在url地址后面以请求字符串的形式传递参数
- http://网址/index.php?name=tom&age=23&addr=DZU
- 蓝色部分就是请求字符串，就是一些“名-值”对，中间使用 & 符号链接
- post方式是把from表单的数据请求出来以XML方式传递给服务器