---
title: "Go IP 段范围校验"
date: 2020-09-08T18:02:00+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "go,ip"
comments: true
weight: 0
tags: ["go","ip"]
categories: ["golang"]
image: "https://static.debuginn.com/202303011905845.jpg"
---

近期做了一个需求，是检测某个 IP 是否在若干 IP 段内，做固定地点 IP 筛查，满足特定业务需求。

## 解决方案

### PLAN A 点分十进制范围区分

简单来讲，就是将 IPv4 原有的四段，分别对比 IP 地址，查看每一段是否在 IP 段范围内，可以用于段控制在每一个特定段 0 ～ 255 内筛选，例如：

```sybase
192.123.1.0 ～ 192.123.156.255 
```

这样的比较规范的特定段可以实现简单的筛选，但是问题来了，不规则的连续 IP 段怎么排除？ 如下：

```sybase
IP段：192.168.1.0 ～ 192.172.3.255
IP： 192.160.0.255
```

这样就会出现问题，可以看到按照简单的分段对比，很明显校验不通过，但是这个 IP 还是存在在 IP 段中，方案只能针对统一分段下规则的IP段才可以区分。

### PLAN B 转整型对别

**IP 地址可以转换为整数，可以将 IP 范围化整为 整数范围进行排查。**

这种方式只需要将授为范围内的地址转换为整数，就可以将 IP 排查在外了。

## 代码

以下是示例代码：

```go
package main

import (
	"fmt"
	"strconv"
	"strings"
)

func main() {
	ipVerifyList := "192.168.1.0-192.172.3.255"
	ip := "192.170.223.1"
	ipSlice := strings.Split(ipVerifyList, `-`)
	if len(ipSlice) < 0 {
		return
	}
	if ip2Int(ip) >= ip2Int(ipSlice[0]) && ip2Int(ip) <= ip2Int(ipSlice[1]) {
		fmt.Println("ip in iplist")
		return
	}
	fmt.Println("ip not in iplist")
}

func ip2Int(ip string) int64 {
	if len(ip) == 0 {
		return 0
	}
	bits := strings.Split(ip, ".")
	if len(bits) < 4 {
		return 0
	}
	b0 := string2Int(bits[0])
	b1 := string2Int(bits[1])
	b2 := string2Int(bits[2])
	b3 := string2Int(bits[3])

	var sum int64
	sum += int64(b0) << 24
	sum += int64(b1) << 16
	sum += int64(b2) << 8
	sum += int64(b3)

	return sum
}

func string2Int(in string) (out int) {
	out, _ = strconv.Atoi(in)
	return
}
```
