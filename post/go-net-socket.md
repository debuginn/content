---
title: "Go 语言使用 net 包实现 Socket 网络编程"
date: 2020-05-25T08:42:00+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "go,net,socket"
comments: true
tags: ["go","net","socket"]
categories: ["golang"]
image: "https://static.debuginn.com/202303031854061.jpg"
---

## TCP/IP

TCP/IP 传输协议，即传输控制/网络协议，也叫作网络通讯协议。它是在网络的使用中的最基本的通信协议。TCP/IP 传输协议对互联网中各部分进行通信的标准和方法进行了规定。并且，TCP/IP 传输协议是保证网络数据信息及时、完整传输的两个重要的协议。TCP/IP 传输协议是严格来说是一个四层的体系结构，应用层、传输层、网络层和数据链路层都包含其中。

### TCP/IP 协议簇常见通信协议

- **应用层**：TFTP，HTTP，SNMP，FTP，SMTP，DNS，Telnet 等等 
- **传输层**：TCP，UDP 
- **网络层**：IP，ICMP，OSPF，EIGRP，IGMP 
- **数据链路层**：SLIP，CSLIP，PPP，MTU

![ip](https://static.debuginn.com/202303032258519.jpg)

![tcp](https://static.debuginn.com/202303032258291.gif)

## Socket

两个进程如果需要进行通讯最基本的一个前提能能够唯一的标示一个进程，在本地进程通讯中我们可以使用 PID 来唯一标示一个进程，但 PID 只在本地唯一，网络中的两个进程 PID 冲突几率很大，这时候我们需要另辟它径了，我们知道 IP 层的 ip 地址可以唯一标示主机，而 TCP 层协议和端口号可以唯一标示主机的一个进程，这样我们可以利用 ip 地址＋协议＋端口号唯一标示网络中的一个进程。

能够唯一标示网络中的进程后，它们就可以利用 socket 进行通信了，什么是socket 呢？我们经常把 socket 翻译为套接字，socket 是在应用层和传输层之间的一个抽象层，它把 TCP/IP 层复杂的操作抽象为几个简单的接口供应用层调用已实现进程在网络中通信。

socket是一种"打开—读/写—关闭"模式的实现，服务器和客户端各自维护一个"文件"，在建立连接打开后，可以向自己文件写入内容供对方读取或者读取对方内容，通讯结束时关闭文件。

Socket 是实现“打开--读/写--关闭”这样的模式，以使用 TCP 协议通讯的 socket 为例。如下图所示：

![socket](https://static.debuginn.com/202303032259498.png)

## TCP 实现

一个 TCP 客户端进行 TCP 通信的流程如下：

- 建立与服务端的链接 
- 进行数据收发 
- 关闭链接

### server 端

```go
package main

import (
	"bufio"
	"fmt"
	"net"
)

func process(conn net.Conn) {
	// 处理完关闭连接
	defer conn.Close()

	// 针对当前连接做发送和接受操作
	for {
		reader := bufio.NewReader(conn)
		var buf [128]byte
		n, err := reader.Read(buf[:])
		if err != nil {
			fmt.Printf("read from conn failed, err:%v\n", err)
			break
		}

		recv := string(buf[:n])
		fmt.Printf("收到的数据：%v\n", recv)

		// 将接受到的数据返回给客户端
		_, err = conn.Write([]byte("ok"))
		if err != nil {
			fmt.Printf("write from conn failed, err:%v\n", err)
			break
		}
	}
}

func main() {
	// 建立 tcp 服务
	listen, err := net.Listen("tcp", "127.0.0.1:9090")
	if err != nil {
		fmt.Printf("listen failed, err:%v\n", err)
		return
	}

	for {
		// 等待客户端建立连接
		conn, err := listen.Accept()
		if err != nil {
			fmt.Printf("accept failed, err:%v\n", err)
			continue
		}
		// 启动一个单独的 goroutine 去处理连接
		go process(conn)
	}
}
```

### client 端

```go
package main

import (
	"bufio"
	"fmt"
	"net"
	"os"
	"strings"
)

func main() {
	// 1、与服务端建立连接
	conn, err := net.Dial("tcp", "127.0.0.1:9090")
	if err != nil {
		fmt.Printf("conn server failed, err:%v\n", err)
		return
	}
	// 2、使用 conn 连接进行数据的发送和接收
	input := bufio.NewReader(os.Stdin)
	for {
		s, _ := input.ReadString('\n')
		s = strings.TrimSpace(s)
		if strings.ToUpper(s) == "Q" {
			return
		}

		_, err = conn.Write([]byte(s))
		if err != nil {
			fmt.Printf("send failed, err:%v\n", err)
			return
		}
		// 从服务端接收回复消息
		var buf [1024]byte
		n, err := conn.Read(buf[:])
		if err != nil {
			fmt.Printf("read failed:%v\n", err)
			return
		}
		fmt.Printf("收到服务端回复:%v\n", string(buf[:n]))
	}
}
```

## UDP 实现

UDP 协议（User Datagram Protocol）中文名称是用户数据报协议，是OSI（Open System Interconnection，开放式系统互联）参考模型中一种**无连接**的传输层协议，不需要建立连接就能直接进行数据发送和接收，属于不可靠的、没有时序的通信，但是UDP协议的实时性比较好，通常用于视频直播相关领域。

### server 端

```go
package main

import (
	"fmt"
	"net"
)

func main() {
	// 建立 udp 服务器
	listen, err := net.ListenUDP("udp", &net.UDPAddr{
		IP:   net.IPv4(0, 0, 0, 0),
		Port: 9090,
	})
	if err != nil {
		fmt.Printf("listen failed error:%v\n", err)
		return
	}
	defer listen.Close() // 使用完关闭服务

	for {
		// 接收数据
		var data [1024]byte
		n, addr, err := listen.ReadFromUDP(data[:])
		if err != nil {
			fmt.Printf("read data error:%v\n", err)
			return
		}
		fmt.Printf("addr:%v\t count:%v\t data:%v\n", addr, n, string(data[:n]))
		// 发送数据
		_, err = listen.WriteToUDP(data[:n], addr)
		if err != nil {
			fmt.Printf("send data error:%v\n", err)
			return
		}
	}
}
```

### client 端

```go
package main

import (
	"fmt"
	"net"
)

func main() {
	// 建立服务
	listen, err := net.DialUDP("udp", nil, &net.UDPAddr{
		IP:   net.IPv4(0, 0, 0, 0),
		Port: 9090,
	})
	if err != nil {
		fmt.Printf("listen udp server error:%v\n", err)
	}
	defer listen.Close()

	// 发送数据
	sendData := []byte("Hello server")
	_, err = listen.Write(sendData) // 发送数据
	if err != nil {
		fmt.Println("发送数据失败，err:", err)
		return
	}

	// 接收数据
	data := make([]byte, 4096)
	n, remoteAddr, err := listen.ReadFromUDP(data) // 接收数据
	if err != nil {
		fmt.Println("接收数据失败，err:", err)
		return
	}
	fmt.Printf("recv:%v addr:%v count:%v\n", string(data[:n]), remoteAddr, n)
}
```

## 参考文章

- [Go语言基础之网络编程 - 李文周的个人博客](https://www.liwenzhou.com/posts/Go/15_socket/)
- [简单理解Socket - 谦行 - 博客园](https://www.cnblogs.com/dolphinX/p/3460545.html) 
- [TCP/IP协议 - 百度百科](https://baike.baidu.com/item/TCP%2FIP%E5%8D%8F%E8%AE%AE?fromId=7729) 
- [详解TCP连接的“三次握手”与“四次挥手”(下)](https://www.cnblogs.com/AhuntSun-blog/p/12037852.html)