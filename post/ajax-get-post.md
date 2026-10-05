---
title: "Ajax Get 和 POST 请求注意事项"
date: 2028-07-22T19:02:15+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "js,ajax,xml,post,get"
comments: true
weight: 0

tags: [ "js","ajax","xml","post","get" ]
categories: [ "js" ]


image: "https://static.debuginn.com/202304131856440.jpg"
imagePreview: "https://static.debuginn.com/202304131856440.jpg"
---

## Ajax中的Get请求

ajax中get请求需要注意两个地方：

1. 在URL地址后面以请求字符串（传递的get参数信息）形式传递数据；
2. 对中文、=、&等特殊符号的处理。

对特殊信息的处理：

在浏览器中通过get请求传递一些特殊符号信息会被误解与混淆，例如& 、 = 等

为了避免特殊符号被误解产生歧义，需要对其进行编码处理。

同时如果传递Get参数有中文信息，也需要进行编码处理。

1. 在PHP里面可以函数`urlencode() / urldecode()` 对特殊符号进行编码、反编码处理
2. 在JavaScript中可以通过`encodeURLComponent()` 对特殊符号等信息进行编码。
（备注：以上蓝色函数可以把“特殊符号、中文”转变为浏览器可以识别不会混淆的信息。编码后的信息为%后接两个十六进制数）

```shell
url参数中有+、空格、=、%、&、#等特殊符号的问题解决？
解决办法： 将这些字符转化成服务器可以识别的字符，对应关系如下： 
URL字符转义 URL 中+号表示空格              %2B   
空格 URL中的空格可以用+号或者编码           %20 
 /   分隔目录和子目录                      %2F     
 ?    分隔实际的URL和参数                  %3F     
 %    指定特殊字符                         %25     
 表示书签                                 %23&   
 URL 中指定的参数间的分隔符                 %26     
 =    URL 中指定参数的值                   %3D
```

## Ajax中的POST请求方式

ajax中POST方式需要注意的四个地方：

1. 给服务器传递数据需要调用send(请求字符串数据)方法 
2. 调用方法setRequestHeader()把传递的数据组织为xml格式（模仿form表单传递数据） 
3. 传递的中文信息无需编码，特殊符号&、| 仍需要进行编码 
4. 该方式请求的同时也可以传递get参数信息，同样使用$_GET接收该信息。

POST方式请求需要把信息组织为请求字符串传递给send()方法