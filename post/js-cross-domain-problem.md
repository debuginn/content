---
title: "JavaScript 跨域问题"
date: 2018-07-18T19:05:38+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "js,cross"
comments: true
weight: 0
tags: [ "js","cross" ]
categories: [ "js" ]
image: "https://webp.debuginn.com/202304131856440.jpg"
---

## JS跨域

**跨域**，指的是浏览器不能执行其他网站的脚本。 它是由浏览器的同源策略造成的，是浏览器施加的安全限制。

JavaScript处于安全方面的考虑，不允许跨域调用其他页面的对象。

- http://debuginn.com/a.html调用http://debuginn.com/b.php    （非跨域）
- http://debuginn.com/a.html调用http://baidu.link/b.php     （跨域）
- http://debuginn.com/a.html调用http://a.debuginn.com/b.php  （跨域）
- http://debuginn.com/a.html调用http://debuginn.com:81/b.php （跨域）
- http://debuginn.com/a.html调用https://debuginn.com/b.php   （跨域）

### 跨域解决方法一 — 代理

### 跨域解决方法二 — JSONP

- JSONP用于解决主流浏览器的跨域数据访问的问题。 
- JSONP技术仅仅支持GET请求，不支持POST请求。

### 跨域解决方法三 — XHR2

- 在HTML5中提供的XMLHttpREquest Level2已经实现了跨域访问以及其他的一些新功能 
- IE10以下版本均不支持 
- 在服务器端做一些小的改造即可： 
  - `header(‘Access-Control-Allow-Origin:*’)`; 
  - `header(‘Access-Control-Allow-Methods:POST,GET’)`;