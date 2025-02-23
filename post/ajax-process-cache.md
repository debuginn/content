---
title: "Ajax 对缓存的处理"
date: 2017-12-03T23:06:11+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "js,ajax,cache"
comments: true
weight: 0

tags: [ "js","ajax","cache" ]
categories: [ "js" ]


image: "https://webp.debuginn.com/202304131856440.jpg"
imagePreview: "https://webp.debuginn.com/202304131856440.jpg"
---

## 缓存

浏览器的一次请求需要从服务器获得许多css、img、js等相关的文件，如果每次请求都把相关资源文件加载一次，对带宽、服务器资源、用户等待时间都有严重的损耗，浏览器有做优化处理，就是把css、img、js等文件在第一次请求成功后就在本地保留一个缓存备份，后续的每次请辞u就在本身获得相关的缓存资源文件就可以了，可以明显地加快用户的访问速度。

css、img、js等文件可以缓存，但是动态程序文件例如PHP文件不能进行缓存，即使缓存我们也不要其缓存效果。浏览器对动态程序文件缓存的处理解决：

1. 给请求的地址设置随机数【推荐】； 
2. 给动态程序设置header头信息，禁止浏览器对其缓存。

### 给请求的地址设置随机数

```html
<!DOCTYPE html> 
<html lang="en"> 
<head>     
    <meta charset="UTF-8">     
    <title>Ajax对缓存的处理</title>     
    <script type="text/javascript">         
    function f1(){        
        var xhr = new XMLHttpRequest();      
        xhr.open('get', './server.php?'+Math.random());             
        xhr.send(null);         
    }     
    </script> 
</head> 
<body>     
    <h2>Ajax对缓存的处理</h2>     
    <input type="button" value="触发" onclick="f1()">     
    <div id="result"></div> 
</body> 
</html>
```

### 给动态程序设置header头信息

```php
//设置header头禁止浏览器缓存当前页面 
header("Cache-Control:no-cache"); 
header("Pragma:no-cache"); 
header("Expires:-1"); 
$fp = fopen("test.txt","a"); 
fwrite($fp, "php "); fclose($fp);
```