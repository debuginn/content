---
title: "使用 GOTRACEBACK 快速定位你的 Panic"
date: 2024-09-09T19:30:00+08:00
keywords: "go,env,GOTRACEBACK"
comments: true
tags: ["go","env","GOTRACEBACK"]
categories: ["golang"]
image: "https://webp.debuginn.com/20240909WOFpJS.jpg"
---

近期迁移了一个 go 的项目至 k8s 机器上，发现机器不时会自动重启，当想看重启前日志的时候，Goroutine 运行的状态全部都打印了出来，由于公司云平台查看行数限制，看到最后，还是没有想要看到的 panic 的关键堆栈信息。

前期，由于频繁的重启，怀疑是哪里出现了未捕获的 panic 导致的，于是在使用的第三方包 RocketMQ 和 Talos 等 SDK 包进行了生产消费初始化的 recover 并且对项目中 channel 的关键操作中也添加了 recover 捕获，但是并没有解决问题，还是时不时的出现重启。

在查找 Go 官方文档，发现可以设置这个环境变量 GOTRACEBACK 可以控制 panic 发生后堆栈跟踪的打印级别。

## GOTRACEBACK

Go 运行时使用该环境变量来决定在程序崩溃或出现未处理的 panic 时应该输出多少堆栈跟踪信息，它是在运行 Go 程序时通过环境变量传递给 Go 运行时的。

- `none` 当程序崩溃时，不输出任何堆栈信息；
- `single` 仅显示导致崩溃，出现 panic 的 goroutine 的堆栈信息；
- `all` 显示所有的 goroutine 的堆栈信息；
- `system` 显示所有的 goroutine 的堆栈信息，包括运行时内部 goroutine 的信息；
- `crash` 显示所有的 goroutine 的堆栈信息，然后核心转储程序并退出；

[runtime package - runtime - Go Packages](https://pkg.go.dev/runtime)

## 源代码

> 基于 Go 1.20 版本

### 设置 GOTRACEBACK

```go
//go:linkname setTraceback runtime/debug.SetTraceback  
func setTraceback(level string) {  
    var t uint32  
    switch level {  
    case "none":  
       t = 0  
    case "single", "":  
       t = 1 << tracebackShift  
    case "all":  
       t = 1<<tracebackShift | tracebackAll  
    case "system":  
       t = 2<<tracebackShift | tracebackAll  
    case "crash":  
       t = 2<<tracebackShift | tracebackAll | tracebackCrash  
    default:  
       t = tracebackAll  
       if n, ok := atoi(level); ok && n == int(uint32(n)) {  
          t |= uint32(n) << tracebackShift  
       }  
    }  
    // when C owns the process, simply exit'ing the process on fatal errors  
    // and panics is surprising. Be louder and abort instead.    if islibrary || isarchive {  
       t |= tracebackCrash  
    }  
  
    t |= traceback_env  
  
    atomic.Store(&traceback_cache, t)  
}
```

### 获取 GOTRACEBACK

```go
// Keep a cached value to make gotraceback fast,// since we call it on every call to gentraceback.  
// The cached value is a uint32 in which the low bits  
// are the "crash" and "all" settings and the remaining  
// bits are the traceback value (0 off, 1 on, 2 include system).const (  
    tracebackCrash = 1 << iota  
    tracebackAll    tracebackShift = iota  
)  
  
var traceback_cache uint32 = 2 << tracebackShift  
var traceback_env uint32  
  
// gotraceback returns the current traceback settings.//  
// If level is 0, suppress all tracebacks.  
// If level is 1, show tracebacks, but exclude runtime frames.  
// If level is 2, show tracebacks including runtime frames.  
// If all is set, print all goroutine stacks. Otherwise, print just the current goroutine.  
// If crash is set, crash (core dump, etc) after tracebacking.//  
//go:nosplit  
func gotraceback() (level int32, all, crash bool) {  
    gp := getg()  
    t := atomic.Load(&traceback_cache)  
    crash = t&tracebackCrash != 0  
    all = gp.m.throwing >= throwTypeUser || t&tracebackAll != 0  
    if gp.m.traceback != 0 {  
       level = int32(gp.m.traceback)  
    } else if gp.m.throwing >= throwTypeRuntime {  
       // Always include runtime frames in runtime throws unless  
       // otherwise overridden by m.traceback.       level = 2  
    } else {  
       level = int32(t >> tracebackShift)  
    }  
    return  
}
```

### 基于 GOTRACEBACK 打印堆栈信息

```go
// gp is the crashing g running on this M, but may be a user G, while getg() is  
// always g0.  
func dopanic_m(gp *g, pc, sp uintptr) bool {  
    if gp.sig != 0 {  
       signame := signame(gp.sig)  
       if signame != "" {  
          print("[signal ", signame)  
       } else {  
          print("[signal ", hex(gp.sig))  
       }  
       print(" code=", hex(gp.sigcode0), " addr=", hex(gp.sigcode1), " pc=", hex(gp.sigpc), "]\n")  
    }  
  
    level, all, docrash := gotraceback()  
    if level > 0 {  
       if gp != gp.m.curg {  
          all = true  
       }  
       if gp != gp.m.g0 {  
          print("\n")  
          goroutineheader(gp)  
          traceback(pc, sp, 0, gp)  
       } else if level >= 2 || gp.m.throwing >= throwTypeRuntime {  
          print("\nruntime stack:\n")  
          traceback(pc, sp, 0, gp)  
       }  
       if !didothers && all {  
          didothers = true  
          tracebackothers(gp)  
       }  
    }  
    unlock(&paniclk)  
  
    if panicking.Add(-1) != 0 {  
       // Some other m is panicking too.  
       // Let it print what it needs to print.       // Wait forever without chewing up cpu.       // It will exit when it's done.       lock(&deadlock)  
       lock(&deadlock)  
    }  
  
    printDebugLog()  
  
    return docrash  
}
```


## 测试

```go
package main  
  
import (  
    "fmt"  
    "os"    "time")  
  
func main() {  
  
    env := os.Getenv("GOTRACEBACK")  
    fmt.Printf("GOTRACEBACK: %s\n", env)  
  
    for i := 0; i < 3; i++ {  
       go a()  
    }  
    go b()  
    for i := 0; i < 3; i++ {  
       go a()  
    }  
  
    time.Sleep(time.Second * 1)  
}  
  
func a() {  
    time.Sleep(time.Millisecond * 1)  
    fmt.Printf("aaaaaaa\n")  
}  
  
func b() {  
    time.Sleep(time.Millisecond * 1)  
    panic("b panic ......")  
}
```

### All 显示所有信息

![20240827iHys3u](https://webp.debuginn.com/20240827iHys3u.png)

可以看到，运行时所有的 Goroutine 信息都被打印了出来。

### None 不输出任何信息

![20240827iDAwRU](https://webp.debuginn.com/20240827iDAwRU.png)

设置为 none 后，只会将运行信息打印出来，非用户打印信息不会抛出。


### Single 只显示导致崩溃的 Goroutine 信息

这个设置参数也是默认的设置：

![20240827t9D7HY](https://webp.debuginn.com/20240827t9D7HY.png)

这里只会显示导致 panic 的 goroutine 的堆栈信息以及运行状态。