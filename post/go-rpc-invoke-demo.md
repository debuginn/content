---
title: "Go 语言实现 RPC 调用"
date: 2020-08-01T20:22:00+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "go,rpc,invoke"
comments: true
tags: ["go","rpc","invoke"]
categories: ["golang"]
image: "https://webp.debuginn.com/202303011930703.jpg"
---

## RPC

> 在分布式计算，远程过程调用（英语：Remote Procedure Call，缩写为 RPC）是一个计算机通信协议。该协议允许运行于一台计算机的程序调用另一个地址空间（通常为一个开放网络的一台计算机）的子程序，而程序员就像调用本地程序一样，无需额外地为这个交互作用编程（无需关注细节）。RPC 是一种服务器-客户端（ Client/Server ）模式，经典实现是一个通过 发送请求-接受回应 进行信息交互的系统。
> wiki 维基百科

在这里引用一下维基百科对于 RPC 的解释， 可以针对与 HTTP 协议来比较分析，RPC 更适合于公司中大、中型项目分布式调用场景。

### 调用流程

1. 客户端调用客户端 stub（client stub）。这个调用是在本地，并将调用参数 push 到栈（stack）中; 
2. 客户端 stub（client stub）将这些参数包装，并通过系统调用发送到服务端机器。打包的过程叫 marshalling。（常见方式：XML、JSON、二进制编码）;
3. 客户端本地操作系统发送信息至服务器。（可通过自定义TCP协议或HTTP传输）; 
4. 服务器系统将信息传送至服务端stub（server stub）; 
5. 服务端stub（server stub）解析信息。该过程叫 unmarshalling; 
6. 服务端stub（server stub）调用程序，并通过类似的方式返回给客户端。

![C/S 架构调用](https://webp.debuginn.com/202303011932520.png)

## RPC 与 HTTP 区别

RPC 调用实现的方式是和 HTTP 有异曲同工之处的，但是对于 RPC 与 HTTP 在 请求 / 响应中还是存在着差别的：

1. HTTP 与 RPC 协议在实现上是不同的，大家都了解到 HTTP 原理就是 客户端请求服务端，服务端去响应并返回结果，但是 RPC 协议设计的时候采用的方式就是服务端给客户端提供 TCP 长连接服务，Client 端去调用 Server 提供的接口，实现特定的功能； 
2. RPC 可以同时提供同步调用及异步调用，而 HTTP 提供的方式就是同步调用，客户端会等待并接受服务端的请求处理的结果； 
3. RPC 服务设计可以提高代码编写过程中的解耦操作，提高代码的可移植性，每一个 服务可以设计成提供特定功能的小服务，客户端去调取远程的服务，而不用去关心远程是怎么实现的。

### RPC 应用领域

- 大型网站的内部子系统设计； 
- 为系统提供降级功能； 
- 并发设计场景；

当然 RPC 也有缺点，每一个 RPC 服务都需要单独搭建，一旦服务出错或者更为严重的不提供支持，作为客户端的就会出现服务不可用，这对系统稳定性及可持续提供支持要求比较高，当然在设计过程中，这样也加大了对系统调试的难度，也就是说这种设计要求 RPC 服务的稳定性及正确性要求是比较大的。

## 实现代码

### 客户端实现

```go
package main

import (
	"demo/common"
	"fmt"
	"net/rpc"
)

func main() {
	var args = common.Args{A: 32, B: 14}
	var result = common.Result{}

	var client, err = rpc.DialHTTP("tcp", "127.0.0.1:9090")
	if err != nil {
		fmt.Printf("connect rpc server failed, err:%v", err)
	}

	err = client.Call("MathService.Divide", args, &result)
	if err != nil {
		fmt.Printf("call math service failed, err:%v", err)
	}
	fmt.Printf("call RPC server success, result:%f", result.Value)
}
```

### 服务端实现

```go
package main

import (
	"demo/common"
	"fmt"
	"net/http"
	"net/rpc"
)

func main() {
	var ms = new(common.MathService)
	// 注册 RPC 服务
	err := rpc.Register(ms)
	if err != nil {
		fmt.Printf("rpc server register faild, err:%s", err)
	}
	// 将 RPC 服务绑定到 HTTP 服务中去
	rpc.HandleHTTP()

	fmt.Printf("server start ....")
	err = http.ListenAndServe(":9090", nil)

	if err != nil {
		fmt.Printf("listen and server is failed, err:%v\n", err)
	}

	fmt.Printf("server stop ....")
}
```

### 功能实现

```go
package common

import "errors"

type Args struct {
	A, B float32
}

type Result struct {
	Value float32
}

type MathService struct {}


func (s *MathService) Add (args *Args, result *Result) error{
	result.Value = args.A + args.B
	return nil
}


func (s *MathService) Divide(args *Args, result *Result) error{
	if args.B == 0 {
		return errors.New("arge.B is 0")
	}

	result.Value = args.A / args.B
	return nil
}
```

## References

- [简述RPC原理实现 - 博客园](https://www.cnblogs.com/sanshengshui/p/9769517.html)
- Http和RPC区别 
- [远程过程调用 - 维基百科](https://zh.wikipedia.org/wiki/%E9%81%A0%E7%A8%8B%E9%81%8E%E7%A8%8B%E8%AA%BF%E7%94%A8)
- [直观讲解--RPC调用和HTTP调用的区别](https://blog.csdn.net/m0_38110132/article/details/81481454)