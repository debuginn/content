---
title: "Go 标准库 限流器 time/rate 设计与实现"
date: 2020-08-24T19:35:00+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "go,standard lib,time rate"
comments: true
tags: ["go","standard lib","time rate"]
categories: ["golang"]
image: "https://webp.debuginn.com/202303011912531.jpg"
---

限流器是后台服务中十分重要的组件，在实际的业务场景中使用居多，其设计在微服务、网关、和一些后台服务中会经常遇到。限流器的作用是用来限制其请求的速率，保护后台响应服务，以免服务过载导致服务不可用现象出现。

**限流器**的实现方法有很多种，例如 Token Bucket、滑动窗口法、Leaky Bucket等。

在 Golang 库中官方给我们提供了限流器的实现`golang.org/x/time/rate`，它是基于令牌桶算法（Token Bucket）设计实现的。

## 令牌桶算法

令牌桶设计比较简单，可以简单的理解成一个只能存放固定数量雪糕?的一个冰箱，每个请求可以理解成来拿雪糕的人，有且只能每一次请求拿一块?，那雪糕拿完了会怎么样呢？这里会有一个固定放雪糕的工人，并且他往冰箱里放雪糕的频率都是一致的，例如他 1s 中只能往冰箱里放 10 块雪糕，这里就可以看出请求响应的频率了。

**令牌桶设计概念：**

- **令牌**：每次请求只有拿到 Token 令牌后，才可以继续访问； 
- **桶**：具有固定数量的桶，每个桶中最多只能放设计好的固定数量的令牌； 
- **入桶频率**：按照固定的频率往桶中放入令牌，放入令牌不能超过桶的容量。

也就是说，基于令牌桶设计算法就限制了请求的速率，达到请求响应可控的目的，特别是针对于高并发场景中突发流量请求的现象，后台就可以轻松应对请求了，因为到后端具体服务的时候突发流量请求已经经过了限流了。

## 具体设计

### 限流器定义

```go
type Limiter struct {
	mu        sync.Mutex // 互斥锁（排他锁）
	limit     Limit      // 放入桶的频率  float64 类型
	burst     int        // 桶的大小
	tokens    float64    // 令牌 token 当前剩余的数量
	last      time.Time  // 最近取走 token 的时间
	lastEvent time.Time  // 最近限流事件的时间
}
```

limit、burst 和 token 是这个限流器中核心的参数，请求并发的大小在这里实现的。

在令牌发放之后，会存储在 Reservation 预约对象中：

```go
type Reservation struct {
	ok        bool      // 是否满足条件分配了 token
	lim       *Limiter  // 发送令牌的限流器
	tokens    int       // 发送 token 令牌的数量
	timeToAct time.Time // 满足令牌发放的时间
	limit     Limit     // 令牌发放速度
}
```

### 消费 Token

Limiter 提供了三类方法供用户消费 Token，用户可以每次消费一个 Token，也可以一次性消费多个 Token。而每种方法代表了当 Token 不足时，各自不同的对应手段。

#### Wait、WaitN

```go
func (lim *Limiter) Wait(ctx context.Context) (err error)
func (lim *Limiter) WaitN(ctx context.Context, n int) (err error)
```

其中，Wait 就是 `WaitN(ctx, 1)`，在下面的方法介绍实现也是一样的。

使用 Wait 方法消费 Token 时，如果此时桶内 Token 数组不足 ( 小于 n  )，那么 Wait 方法将会阻塞一段时间，直至 Token 满足条件。如果充足则直接返回。

#### Allow、AllowN

```go
func (lim *Limiter) Allow() bool
func (lim *Limiter) AllowN(now time.Time, n int) bool 
```

AllowN 方法表示，截止到当前某一时刻，目前桶中数目是否至少为 n 个，满足则返回 true，同时从桶中消费 n 个 token。
反之返回不消费 Token，false。

通常对应这样的线上场景，如果请求速率过快，就直接丢到某些请求。

#### Reserve、ReserveN

官方提供的限流器有阻塞等待式的 Wait，也有直接判断方式的 Allow，还有提供了自己维护预留式的，但核心的实现都是下面的 reserveN 方法。

```go
func (lim *Limiter) Reserve() *Reservation
func (lim *Limiter) ReserveN(now time.Time, n int) *Reservation
```

当调用完成后，无论 Token 是否充足，都会返回一个Reservation *对象。

你可以调用该对象的 Delay() 方法，该方法返回了需要等待的时间。如果等待时间为 0，则说明不用等待。
必须等到等待时间结束之后，才能进行接下来的工作。

或者，如果不想等待，可以调用 Cancel() 方法，该方法会将 Token 归还。

```go
func (lim *Limiter) reserveN(now time.Time, n int, maxFutureReserve time.Duration) Reservation {
	lim.mu.Lock()

	// 首先判断是否放入频率是否为无穷大
	// 如果为无穷大，说明暂时不限流
	if lim.limit == Inf {
		lim.mu.Unlock()
		return Reservation{
			ok:        true,
			lim:       lim,
			tokens:    n,
			timeToAct: now,
		}
	}

	// 拿到截至 now 时间时
	// 可以获取的令牌 tokens 数量及上一次拿走令牌的时间 last
	now, last, tokens := lim.advance(now)

	// 更新 tokens 数量
	tokens -= float64(n)

	// 如果 tokens 为负数，代表当前没有 token 放入桶中
	// 说明需要等待，计算等待的时间
	var waitDuration time.Duration
	if tokens < 0 {
		waitDuration = lim.limit.durationFromTokens(-tokens)
	}

	// 计算是否满足分配条件
	// 1、需要分配的大小不超过桶的大小
	// 2、等待时间不超过设定的等待时长
	ok := n <= lim.burst && waitDuration <= maxFutureReserve

	// 预处理 reservation
	r := Reservation{
		ok:    ok,
		lim:   lim,
		limit: lim.limit,
	}
	// 若当前满足分配条件
	// 1、设置分配大小
	// 2、满足令牌发放的时间 = 当前时间 + 等待时长
	if ok {
		r.tokens = n
		r.timeToAct = now.Add(waitDuration)
	}

	// 更新 limiter 的值，并返回
	if ok {
		lim.last = now
		lim.tokens = tokens
		lim.lastEvent = r.timeToAct
	} else {
		lim.last = last
	}

	lim.mu.Unlock()
	return r
}
```

## 具体使用

rate 包中提供了对限流器的使用，只需要指定 limit（放入桶中的频率）、burst（桶的大小）。

```go
func NewLimiter(r Limit, b int) *Limiter {
	return &Limiter{
		limit: r, // 放入桶的频率
		burst: b, // 桶的大小
	}
}
```

在这里，使用一个 http api 来简单的验证一下 `time/rate` 的强大：

```go
func main() {
	r := rate.Every(1 * time.Millisecond)
	limit := rate.NewLimiter(r, 10)
	http.HandleFunc("/", func(writer http.ResponseWriter, request *http.Request) {
		if limit.Allow() {
			fmt.Printf("请求成功，当前时间：%s\n", time.Now().Format("2006-01-02 15:04:05"))
		} else {
			fmt.Printf("请求成功，但是被限流了。。。\n")
		}
	})

	_ = http.ListenAndServe(":8081", nil)
}
```

在这里，我把桶设置成了每一毫秒投放一次令牌，桶容量大小为 10，起一个 http 的服务，模拟后台 API。

接下来做一个压力测试，看看效果如何：

```go
func GetApi() {
	api := "http://localhost:8081/"
	res, err := http.Get(api)
	if err != nil {
		panic(err)
	}
	defer res.Body.Close()

	if res.StatusCode == http.StatusOK {
		fmt.Printf("get api success\n")
	}
}

func Benchmark_Main(b *testing.B) {
	for i := 0; i < b.N; i++ {
		GetApi()
	}
}
```

效果如下：

```sybase
......
请求成功，当前时间：2020-08-24 14:26:52
请求成功，但是被限流了。。。
请求成功，但是被限流了。。。
请求成功，但是被限流了。。。
请求成功，但是被限流了。。。
请求成功，但是被限流了。。。
请求成功，当前时间：2020-08-24 14:26:52
请求成功，但是被限流了。。。
请求成功，但是被限流了。。。
请求成功，但是被限流了。。。
请求成功，但是被限流了。。。
......
```

在这里，可以看到，当使用 AllowN 方法中，只有当令牌 Token 生产出来，才可以消费令牌，继续请求，剩余的则是将其请求抛弃，当然在实际的业务处理中，可以用比较友好的方式反馈给前端。

在这里，先有的几次请求都会成功，是因为服务启动后，令牌桶会初始化，将令牌放入到桶中，但是随着突发流量的请求，令牌按照预定的速率生产令牌，就会出现明显的令牌供不应求的现象。

## 开源仓库

目前 `time/rate` 是一个独立的限流器开源解决方案，感兴趣的小伙伴可以给此项目一个 Star，谢谢。

[https://github.com/golang/time](https://github.com/golang/time)

## References

- [限流器系列(2) -- Token Bucket 令牌桶 ](https://www.haohongfan.com/post/2020-06-30-token-bucket/)
- [Golang 限流器的使用和实现](https://segmentfault.com/a/1190000023033365)
- [Golang 标准库限流器 time/rate 使用介绍](https://www.cyhone.com/articles/usage-of-golang-rate/)
- [https://github.com/golang/time/rate.go](https://github.com/golang/time/blob/3af7569d3a1e/rate/rate.go)
