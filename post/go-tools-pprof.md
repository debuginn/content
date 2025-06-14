---
title: "使用 pprof 对 Go 程序进行分析优化"
date: 2022-05-01T18:12:00+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "faas,notes"
comments: true
weight: 0
tags: ["Go","pprof","分析","go pprof","go tools"]
categories: ["golang"]
image: "https://static.debuginn.com/202302262119034.jpg"
---

## 前言

在生产环境中，偶尔会发生 Go 程序 CPU 暴增的现象，排除某时段并发大的场景外，通过监控面板看不到程序是因为什么原因导致的，Go 语言原生就提供了工具 pprof，**Google 对于 pprof 的解释就是一个用于可视化和分析数据的工具。** 通过使用 Go pprof 可以对程序的 CPU性能、内存占用、Goroutine wait share resource、mutex lock 做剖面分析，我们可以使用该工具收集运行时的程序性能指标，从而分析出程序中是否由于代码编写不合理导致存在不合理的资源占用情况，从而对程序进行优化用来提升其性能。

## 功能

Go pprof 提供了以下五种不同维度观测其程序的功能：

- **CPU Profiling**：CPU 性能分析，按照指定时间采集监听其 Go 程序 CPU 的使用情况，可以确定 Go 程序在哪个程序段中占用 CPU 耗时长；
- **Memory Profiling**：内存性能分析，用来分析程序的内存堆栈区使用情况，用来检测是否存在内存泄漏；
- **Block Profiling**：Goroutine 等待共享资源阻塞分析；
- **Mutex Profiling**：互斥锁分析，用来报告共享资源使用互斥锁的竞争的情况；
- **Goroutine Profiling**：协程性能分析，用来报告对当前运行时的 Goroutine 操作及数量。

## 使用

Go pprof 工具的使用也是比较简单快捷的，可以使用runtime/pprof包生成一个 profile 文件，网上也有很多的教程，这里不再过多描述了，详细可以看下包提供的[函数](https://pkg.go.dev/runtime/pprof)，上面介绍了使用方法。

目前我们主要使用的是`net/http/pprof`包，启动一个独立端口号 http 程序单独用来 Go 程序的分析，**搭配着 graphviz 组件来可视化程序来分析数据**，使用起来也是比较方便的：

第一步，将`net/http/pprof`包引用到程序中，建议直接放在程序入口处 `main.go` 文件

```go
import (
    _ "net/http/pprof"
)
```

第二步，若本身是一个 http 的程序，不需要此步骤，若不是 http web 程序或者不想将对应信息暴露在外网，可以单开一个 http web 程序用来专门监听服务：

```go
func main() {
    // 程序逻辑代码
    
    go func() {
        _ = http.ListenAndServe(":8848", nil)
    }()
}
```

第三步，运行主程序，访问 pprof 界面：

```sybase
http://127.0.0.1:8848/debug/pprof/  # 主界面

http://127.0.0.1:8848/debug/pprof/allocs     # 所有过去内存分配的采样
http://127.0.0.1:8848/debug/pprof/block      # 导致同步阻塞的堆栈跟踪 
http://127.0.0.1:8848/debug/pprof/cmdline    # 当前程序的命令行的完整调用路径
http://127.0.0.1:8848/debug/pprof/goroutine  # 所有当前 Goroutine 的堆栈跟踪
http://127.0.0.1:8848/debug/pprof/heap       # 活动对象的内存分配的采样
http://127.0.0.1:8848/debug/pprof/mutex      # 争用互斥锁持有者的堆栈跟踪
http://127.0.0.1:8848/debug/pprof/profile    # CPU 配置文件
http://127.0.0.1:8848/debug/pprof/threadcreate # 创建新 OS 线程的堆栈跟踪
http://127.0.0.1:8848/debug/pprof/trace      # 当前程序执行的跟踪
```

后缀加上 `?debug=1` 可以可视化查看对应描述，不加就可以下载成 profile 文件，使用 pprof 命令可视化查看对应数据。

第四步，使用 `go tool pprof -http=:6001 profile` 命令查看分析程序。

## 分析

![cpu profile](https://static.debuginn.com/202302262125077.png)

上图是针对 CPU 使用做的采集可视化，箭头越粗、方块越大就代表着对应的操作消耗 CPU 大，可以看到占用 CPU 最多的操作就是 json 的序列化和反序列化操作。

同理对应的内存性能、Goroutine 阻塞的分析都可以看出对应的操作。

## 总结

使用 go pprof 工具可以分析解剖程序运行性能问题，可以快速定位生产环境中遇到的问题，并作出优化或者 fix bug，最后祝大家不会写出 bug code，程序稳定、头发永在。