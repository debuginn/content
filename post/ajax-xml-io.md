---
title: "Ajax 对 XML 信息的接收和处理"
date: 2018-10-22T18:54:57+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "js,ajax,xml"
comments: true
weight: 0

tags: ["js","ajax","xml"]
categories: ["js"]


image: "https://webp.debuginn.com/202304131856440.jpg"
imagePreview: "https://webp.debuginn.com/202304131856440.jpg"
---

- Ajax负责请求xml和接收xml信息，dom负责处理xml信息 
- dom： 
  - php中，dom是php与xml（html）之间的沟通桥梁； 
  - javascript中，dom是javascript与html（xml）之间沟通的桥梁。 
- xml需要从服务器端返回到客户端被javascript处理； 
- ajax：负责请求xml回来； 
- DOM（javascript）：负责处理xml信息。

**Ajax+JavaScript实现对xml的接收处理，可以方便我们后期实现一个静态网站（html+css+javascript）实现对各个接口数据的处理。**

document对象和普通元素对象都可以调用getElementsByTagName()方法。

## 函数执行操作

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>利用ajax+javaScript实现对xml的接受和处理</title>
    <script type="text/javascript">
        function f1(){
            //ajax请求xml信息回来
            //javascript的dom技术处理xml
            //document xmldocument
            var xhr = new XMLHttpRequest();
            xhr.onreadystatechange = function(){
                if(xhr.readyState == 4 ){
                    //[object XMLDocument] 其实是xml根结点的父节点对象
                    //alert(xhr.responseXML);
                    var xmldom = xhr.responseXML;

                    //console.log(xmldom.firstChild);<weather>
                    var citys = xmldom.getElementsByTagName('city');

                    //citys[1] //第二个city的元素节点对象
                    /*for(var k in citysp[1]){//k代表元素节点的成员名称
                        //有输出其中一个成员方法：getElementsByTagName
                        //结论：document对象 和 普通元素都可以调用getElementdByTagName函数
                        console.log(k);
                    }*/
                    var str ="";
                    for(var i=0; i<citys.length; i++){
                        var nm = citys[i].getElementsByTagName('name')[0].firstChild.nodeValue;
                        var temp = citys[i].getElementsByTagName('temp')[0].firstChild.nodeValue;
                        var wind = citys[i].getElementsByTagName('wind')[0].firstChild.nodeValue;
                        str += "城市：" +nm+ "--温度："+temp+"--风向："+wind+"<br/>";
                    }
                    document.getElementById('result').innerHTML =str;
                }
            }
            xhr.open('get','./test01.xml');
            xhr.send(null);
        }
    </script>
</head>
<body>
    <h2>利用ajax+javaScript实现对xml的接受和处理</h2>
    <input type="button" value="触发" onclick="f1()">
    <div id="result"></div>
</body>
</html>
```

## XML文件数据

```xml
<?xml version="1.0" encoding="utf-8" ?>
<weather>
    <city>
        <name>北京</name>
        <temp>23-31度</temp>
        <wind>北风</wind>
    </city>
    <city>
        <name>上海</name>
        <temp>23-31度</temp>
        <wind>东风</wind>
    </city>
    <city>
        <name>广州</name>
        <temp>28-31度</temp>
        <wind>南风</wind>
    </city>
    <city>
        <name>深圳</name>
        <temp>29-31度</temp>
        <wind>东南风</wind>
    </city>
</weather>
```