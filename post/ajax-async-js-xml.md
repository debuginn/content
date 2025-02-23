---
title: "Ajax 异步的JavaScript与XML技术"
date: 2018-07-17T19:09:35+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "ajax,js,xml"
comments: true
weight: 0

tags: [ "ajax","js","xml" ]
categories: [ "js" ]


image: "https://webp.debuginn.com/202304131856440.jpg"
imagePreview: "https://webp.debuginn.com/202304131856440.jpg"
---

## Ajax 技术简介

AJAX即“Asynchronous JavaScript and XML”（异步的JavaScript与XML技术），指的是一套综合了多项技术的浏览器端网页开发技术。Ajax的概念由杰西·詹姆士·贾瑞特所提出。传统的Web应用允许用户端填写表单（form），当提交表单时就向网页服务器发送一个请求。服务器接收并处理传来的表单，然后送回一个新的网页，但这个做法浪费了许多带宽，因为在前后两个页面中的大部分HTML码往往是相同的。由于每次应用的沟通都需要向服务器发送请求，应用的回应时间依赖于服务器的回应时间。这导致了用户界面的回应比本机应用慢得多。与此不同，AJAX应用可以仅向服务器发送并取回必须的数据，并在客户端采用JavaScript处理来自服务器的回应。因为在服务器和浏览器之间交换的数据大量减少，服务器回应更快了。同时，很多的处理工作可以在发出请求的客户端机器上完成，因此Web服务器的负荷也减少了。

## JSON技术

[JavaScript 对象表示法JSON](/p/js-json/)

## 用jQuery实现Ajax

- `jQuery.ajax([settings])`
- type：类型，“POST”或“GET”，默认为“GET” 
- url：发送请求的地址 
- data：是一个对象，联通请求的发送到服务器中的数据； 
- dataType：预期服务器返回的数据类型。如果不确定，jQuery将自动根据HTTP包MIME信息来只能判断，一般采用json格式，将其设置为“JSON”； 
- success：是一个方法请求成功后的回调函数，传入返回后的数据，以及包含成功代码的字符串； 
- error：是一个方法，请求失败时调用此函数，传入XMLHttpRequest对象。