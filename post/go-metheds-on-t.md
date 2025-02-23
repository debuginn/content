---
title: "[译] 方法是否应该在 T 或 *T 上声明"
date: 2021-06-27T23:18:00+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "go,translate"
comments: true
tags: ["go","translate"]
categories: ["golang"]
image: "https://webp.debuginn.com/202302272119360.png"
---

译文原地址：[Should methods be declared on T or *T - David](https://dave.cheney.net/2016/03/19/should-methods-be-declared-on-t-or-t)

在 Go 中，对于任何的类型 T，都存在一个类型 *T，他是一个表达式的结果，该表达式接收的是类型 T ，例如：

```go
type T struct { a int; b bool } 
var t T    // t's type is T 
var p = &t // p's type is *T
```

这两种类型，T 和 *T 是不同的，但 *T 不能代替 T。

你可以在你拥有的任意类型上声明一个方法；也就是说，在您的包中的函数声明的类型。因此，您可以在声明的类型 T 和对应的派生指针类型 *T 上声明方法。另一种说法是，类型上的方法被声明为接收器接收者值的副本，或一个指向其接收者值的指针。所以问题就存在了，究竟是哪种形式最合适？

显然，如果你的方法改变了他的接收者，他应该在 *T 上声明。但是，如果方法不改变他的接收者，在 T 上声明它是安全的么？

事实证明，这样做的话安全的情况非常有限（简单理解就是不安全的）。例如，众所周知，你不应该复制一个 sync.Mutex 的值，因为它打破了互斥量的不变量。由于互斥锁控制对变量（共享资源）的访问，他们经常被包装在一个结构体中，包含他们的控制的值（共享资源）：

```go
package counter

type Val struct {
    mu  sync.Mutex
    val int
}

func (v *Val) Get() int {
    v.mu.Lock()
    defer v.mu.Unlock()
    return v.val
}

func (v *Val) Add(n int) {
    v.mu.Lock()
    defer v.mu.Unlock()
    v.val += n
}
```

大部分 Gopher 都知道，忘记在指针接收器 *Val 上是声明 Get 或 Add 方法是错误的。然而，任何嵌入 Val 来利用其 0 值的类型，也必须仅在其指针接收器上声明方法，否者可能会无意复制其嵌入类型值的内容：

```go
type Stats struct {
    a, b, c counter.Val
}

func (s Stats) Sum() int {
    return s.a.Get() + s.b.Get() + s.c.Get() // whoops（哎呀）
}
```

维护值切片的类型可能会出现类似的陷阱，当然也有可能发生意外的数据竞争。

简而言之，我认为您更应该喜欢在 *T 上声明方法，除非您有非常充分的理由不该这样做。

1. 我们说 T 但这只是您声明的类型的占位符； 
2. 此规则是递归的，取 *T 类型的变量的地址返回的是 **T 类型的结果； 
3. 这就是为什么没有人可以在像 int 这样的基础类型上声明方法； 
4. Go 中的方法只是将接受者作为第一个形式参数传递的函数的语法糖；

如果方法不改变它的接收者，它是否需要是一个方法吗？

相关文章：

- [What is the zero value, and why is it useful?](https://dave.cheney.net/2013/01/19/what-is-the-zero-value-and-why-is-it-useful)
- [Ice cream makers and data races](https://dave.cheney.net/2014/06/27/ice-cream-makers-and-data-races)
- [Slices from the ground up](https://dave.cheney.net/2018/07/12/slices-from-the-ground-up)
- [The empty struct](https://dave.cheney.net/2014/03/25/the-empty-struct)

最后，此篇文章我是第一次尝试翻译英文文章，尽管英文水平不太好，一些单词不认识，但是相信自己翻译一篇文章可以学习英语与理解 Go 设计获取 double 的乐趣。