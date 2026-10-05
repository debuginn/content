---
title: "Restful API 设计指北"
date: 2022-03-13T21:35:12+08:00
draft: false
keywords: "api,restful api"
comments: true
tags: ["api","restful api"]
categories: ["debuginn"]
image: "https://static.debuginn.com/202303132137265.jpg"
---

近期学习了 Go 语言，跟着七米在学习，学习过程中了解到了 API 的一个设计规范，也就是本文要讲的 Restful API 设计模式，现在互联网处在前后端分离的阶段，API 的书写及规范化是非常重要的，针对于 API 中 Restful API 中设计比较规范的是 Github API，可以直接访问他们的 [https://api.github.com](https://api.github.com) 直接查看 Github 针对与公共接口的链接及使用方法。

此篇文章也是针对于这几天学习 Restful API 做了一个笔记或小结，若有不足之处还望批评指正，谢谢。

## 使用 HTTPS 协议

这个协议使用本身与这个 API 设计标准没有什么直接联系，使用 HTTPS 协议主要目的是将用户客户端与 API 服务器连接过程中保证其数据的安全性。

注意：由于 API 接口使用 HTTPS 协议，**不要让非 SSL 的链接访问重定向到 SSL 的链接**。

## API 地址和版本问题

为 API 使用专门子域名比较友好，例如使用如下链接使用：

```shell
https://api.debuginn.com
```

也可以将 API 放在主域名下，例如：

```shell
https://debuginn.com/api/
```

当然，针对于 API 版本问题针对以上两种方法可以分别使用如下例子：

```shell
# 针对于 API 子域名方式 api.domain/v1/
https://api.debuginn.com/v1/
# 针对于 主域名目录方式 domain/api/v1/
https://debuginn.com/api/v1/
```

## Schema 响应数据模式

现在前后端分离项目使用的数据响应模式大部分采用的是 JSON 格式数据，也有一些项目采用 XML 格式的数据。

针对于用户客户端请求，服务器响应尽量有 状态码 Status Code 及详细解释。

## 使用正确的 Method

使用正确的 Method 也就是使用正确的 HTTP 请求动词，即 HTTP 协议规定的常常使用的六种请求动词，并针对请求 SQL 语句辅助理解：

```
GET   请求 => SELECT 从服务端获取数据
POST  请求 => CREATE 从服务端创建数据
PUT   请求 => UPDATE 从服务端更新数据（将所有数据元素全部替换掉）
PATCH 请求 => UPDATE 从服务端更新数据（将部分数据元素替换掉）
DELETE请求 => DELETE 从服务端删除数据
```

还有两个不常使用的请求：

```
HEAD    获取资源的元数据。
OPTIONS 获取信息，关于资源的哪些属性是客户端可以改变的。
```

**注意：更新和创建操作应该返回最新的资源，来通知用户资源的情况；删除资源一般不会返回内容。**

## 过滤信息

针对用户端查询数据，需要服务端查询对应数据，包括了筛选、分页等操作。

### 筛选操作

```
api.domain/user/limit/10   指定返回记录的数量;
api.domain/user/offset/10  指定返回记录的开始位置;
api.domain/user/animal_type_id/1      指定筛选条件
api.domain/user/page/2/per_page/100   指定第几页，以及每页的记录数;
api.domain/user/sortby/name/order/asc 指定返回结果按照哪个属性排序，以及排序顺序
```

### 分页操作

当返回某个资源的列表时，如果要返回的数目特别多，比如 github 的 `/users`，就需要使用分页分批次按照需要来返回特定数量的结果。

分页的实现会用到上面提到的 url query，通过两个参数来控制要返回的资源结果：

- per_page：每页返回多少资源，如果没提供会使用预设的默认值；这个数量也是有一个最大值，不然用户把它设置成一个非常大的值（比如99999999）也失去了设计的初衷。 
- page：要获取哪一页的资源，默认是第一页。

## 状态码 Status Code

HTTP 应答中，需要带一个很重要的字段：status code。它说明了请求的大致情况，是否正常完成、需要进一步处理、出现了什么错误，对于客户端非常重要。状态码都是三位的整数，大概分成了几个区间：

- 2XX：请求正常处理并返回 
- 3XX：重定向，请求的资源位置发生变化 
- 4XX：客户端发送的请求有错误 
- 5XX：服务器端错误

在 HTTP API 设计中，经常用到的状态码以及它们的意义如下表：

| 状态码 | LABEL                  | 解释                                                                                                            |
|-----|------------------------|---------------------------------------------------------------------------------------------------------------|
| 200 | OK                     | 请求成功接收并处理，一般响应中都会有 body                                                                                       |
| 201 | Created                | 请求已完成，并导致了一个或者多个资源被创建，最常用在 POST 创建资源的时候                                                                       |
| 202 | Accepted               | 请求已经接收并开始处理，但是处理还没有完成。一般用在异步处理的情况，响应 body 中应该告诉客户端去哪里查看任务的状态                                                  |
| 204 | No Content             | 请求已经处理完成，但是没有信息要返回，经常用在 PUT 更新资源的时候（客户端提供资源的所有属性，因此不需要服务端返回）。如果有重要的 metadata，可以放到头部返回                         |
| 301 | Moved Permanently      | 请求的资源已经永久性地移动到另外一个地方，后续所有的请求都应该直接访问新地址。服务端会把新地址写在 `Location` 头部字段，方便客户端使用。允许客户端把 POST 请求修改为 GET。              |
| 304 | Not Modified           | 请求的资源和之前的版本一样，没有发生改变。用来缓存资源，和条件性请求（conditional request）一起出现                                                   |
| 307 | Temporary Redirect     | 目标资源暂时性地移动到新的地址，客户端需要去新地址进行操作，但是不能修改请求的方法。                                                                    |
| 308 | Permanent Redirect     | 和 301 类似，除了客户端不能修改原请求的方法                                                                                      |
| 400 | Bad Request            | 客户端发送的请求有错误（请求语法错误，body 数据格式有误，body 缺少必须的字段等），导致服务端无法处理                                                       |
| 403 | Forbidden              | 服务器端接收到并理解客户端的请求，但是客户端的权限不足。比如，普通用户想操作只有管理员才有权限的资源。                                                           |
| 404 | Not Found              | 客户端要访问的资源不存在，链接失效或者客户端伪造 URL 的时候会遇到这个情况                                                                       | 
| 405 | Method Not Allowed     | 服务端接收到了请求，而且要访问的资源也存在，但是不支持对应的方法。服务端必须返回`Allow`头部，告诉客户端哪些方法是允许的                                               | 
| 415 | Unsupported Media Type | 服务端不支持客户端请求的资源格式，一般是因为客户端在`Content-Type`或者`Content-Encoding`中申明了希望的返回格式，但是服务端没有实现。比如，客户端希望收到xml返回，但是服务端支持Json |
| 429 | Too Many Requests      | 客户端在规定的时间里发送了太多请求，在进行限流的时候会用到                                                                                 | 
| 500 | Internal Server Error  | 服务器内部错误，导致无法完成请求的内容                                                                                           | 
| 503 | Service Unavailable    | 服务器因为负载过高或者维护，暂时无法提供服务。服务器端应该返回 Retry-After 头部，告诉客户端过一段时间再来重试                                                 | 

针对于状态码，请看此文章：[HTTP常见状态码](https://blog.debuginn.com/p/http-status-code/)

## 错误处理

如果出错的话，在 response body 中通过 `message` 给出明确的信息。

比如客户端发送的请求有错误，一般会返回4XX Bad Request结果。这个结果很模糊，给出错误 message 的话，能更好地让客户端知道具体哪里有问题，进行快速修改。

如果请求的 JSON 数据无法解析，会返回Problems parsing JSON；

如果缺少必要的 filed，会返回422 Unprocessable Entity，除了 message 之外，还通过errors给出了哪些 field 缺少了，能够方便调用方快速排错。

基本的思路就是尽可能提供更准确的错误信息：比如数据不是正确的 json，缺少必要的字段，字段的值不符合规定…… 而不是直接说“请求错误”之类的信息。

## 验证及授权

一般来说，让任何人随意访问公开的 API 是不好的做法。验证和授权是两件事情：

- 验证（Authentication）是为了确定用户是其声明的身份，比如提供账户的密码。不然的话，任何人伪造成其他身份（比如其他用户或者管理员）是非常危险的 
- 授权（Authorization）是为了保证用户有对请求资源特定操作的权限。比如用户的私人信息只能自己能访问，其他人无法看到；有些特殊的操作只能管理员可以操作，其他用户有只读的权限等等

如果没有通过验证（提供的用户名和密码不匹配，token 不正确等），需要返回`401 Unauthorized`状态码，并在 body 中说明具体的错误信息；而没有被授权访问的资源操作，需要返回`403 Forbidden`状态码，还有详细的错误信息。

**注意：对某些用户未被授权访问的资源操作返回`404 Not Found`，目的是为了防止私有资源的泄露（比如黑客可以自动化试探用户的私有资源，返回 403 的话，就等于告诉黑客用户有这些私有的资源，无异于是给黑客提供了方向）。**

## Hypermedia API

RESTful API 最好做到 Hypermedia，即返回结果中提供链接，连向其他API方法，使得用户不查文档，也知道下一步应该做什么。

比如访问 api.github.com，就可以看到 Github API 支持的资源操作。

## 易读的 API 接口文档

API 最终是给人使用的，不管是公司内部，还是公开的 API 都是一样。即使我们遵循了上面提到的所有规范，设计的 API 非常优雅，用户还是不知道怎么使用我们的 API。最后一步，但非常重要的一步是：为你的 API 编写优秀的文档。

**注意：对每个请求以及返回的参数给出说明，最好给出一个详细而完整的示例。**

## 参考资料

- [RESTful API 设计指南 - 阮一峰](http://www.ruanyifeng.com/blog/2014/05/restful_api.html)
- [跟着 Github 学习 Restful HTTP API 设计](https://cizixs.com/2016/12/12/restful-api-design-guide/)
- [REST API Tutorial](https://restfulapi.net/) 
- [Representational State Transfer (REST) - Roy Fielding](https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm)
