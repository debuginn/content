---
title: "Go 并发编程之 Mutex"
date: 2020-11-15T20:34:12+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "faas,notes"
comments: true
weight: 0
tags: ["go","concurrence","Mutex"]
categories: ["golang"]
image: "https://webp.debuginn.com/202302282003561.jpg"
---

我们比较常见的大型项目的设计中都会出现并发访问问题，并发就是为了解决数据的准确性，保证同一个临界区的数据只能被一个线程进行操作，日常中使用到的并发场景也是很多的：

- **计数器**：计数器结果不准确； 
- **秒杀系统**：由于同一时间访问量比较大，导致的超卖； 
- **用户账户异常**：同一时间支付导致的账户透支； 
- **buffer 数据异常**：更新 buffer 导致的数据混乱。

上面都是并发带来的数据准确性的问题，决绝方案就是使用互斥锁，也就是今天并发编程中的所要描述的 Mutex 并发原语。

## 实现机制

互斥锁 Mutex 就是为了避免并发竞争建立的并发控制机制，其中有个“临界区”的概念。

> 在并发编程过程中，如果程序中一部分资源或者变量会被并发访问或者修改，为了避免并发访问导致数据的不准确，这部分程序需要率先被保护起来，之后操作，操作结束后去除保护，这部分被保护的程序就叫做临界区。

**使用互斥锁，限定临界区只能同时由一个线程持有**，若是临界区此时被一个线程持有，那么其他线程想进入到这个临界区的时候，就会失败或者等待释放锁，持有此临界区的线程退出，其他线程才有机会获得这个临界区。

![go mutex 临界区示意图](https://webp.debuginn.com/202302282005455.jpg)

Mutex 是 Go 语言中使用最广泛的同步原语，也称为并发原语，**解决的是并发读写共享资源，避免出现数据竞争 data race 问题。**

## 基本使用

互斥锁 Mutex 提供了两个方法 Lock 和 Unlock：进入到临界区使用 Lock 方法加锁，退出临界区使用 Unlock 方法释放锁。

```go
type Locker interface {
    Lock()
    Unlock()
}

func(m *Mutex)Lock()
func(m *Mutex)Unlock()
```

**当一个 goroutine 调用 Lock 方法获取到锁后，其他 goroutine 会阻塞在 Lock 的调用上，直到当前获取到锁的 goroutine 释放锁。**

接下来是一个计数器的例子，是由 100 个 goroutine 对计数器进行累加操作，最后输出结果：

```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	var mu sync.Mutex
	countNum := 0

	// 确认辅助变量是否都执行完成
	var wg sync.WaitGroup

	// wg 添加数目要和 创建的协程数量保持一致
	wg.Add(100)
	for i := 0; i < 100; i++ {
		go func() {
			defer wg.Done()
			for j := 0; j < 1000; j++ {
				mu.Lock()
				countNum++
				mu.Unlock()
			}
		}()
	}
	wg.Wait()
	fmt.Printf("countNum: %d", countNum)
}
```

## 实际使用

很多时候 Mutex 并不是单独使用的，而是嵌套在 Struct 中使用，作为结构体的一部分，**如果嵌入的 struct 有多个字段，我们一般会把 Mutex 放在要控制的字段上面，然后使用空格把字段分隔开来。**

**甚至可以把获取锁、释放锁、计数加一的逻辑封装成一个方法。**

```go
package main
import (
	"fmt"
	"sync"
)

// 线程安全的计数器
type Counter struct {
	CounterType int
	Name        string

	mu    sync.Mutex
	count uint64
}

// 加一方法
func (c *Counter) Incr() {
	c.mu.Lock()
	defer c.mu.Unlock()
	c.count++
}

// 取数值方法 线程也需要受保护
func (c *Counter) Count() uint64 {
	c.mu.Lock()
	defer c.mu.Unlock()
	return c.count
}

func main() {
	// 定义一个计数器
	var counter Counter

	var wg sync.WaitGroup
	wg.Add(100)

	for i := 0; i < 100; i++ {
		go func() {
			defer wg.Done()
			for j := 0; j < 1000; j++ {
				counter.Incr()
			}
		}()
	}
	wg.Wait()

	fmt.Printf("%d\n", counter.Count())
}
```

## 思考问题

Q：你已经知道，如果 Mutex 已经被一个 goroutine 获取了锁，其它等待中的 goroutine 们只能一直等待。那么，等这个锁释放后，等待中的 goroutine 中哪一个会优先获取 Mutex 呢？

A：FIFO，先来先服务的策略，Go 的 goroutine 调度中，会维护一个保障 goroutine 运行的队列，当获取到锁的 goroutine 执行完临界区的操作的时候，就会释放锁，在队列中排在第一位置的 goroutine 会拿到锁进行临界区的操作。

## 实现原理

Mutex 的架构演进目前分为四个阶段：

![Mutex 演化过程](https://webp.debuginn.com/202302282008657.jpg)

- 初版 Mutex：使用一个 flag 变量表示锁?是否被持有； 
- **给新人机会**：照顾新来的 goroutine 先获取到锁； 
- **多给些机会**：照顾新来的和被唤醒的 goroutine 获取到锁； 
- **解决饥饿**：存在竞争关系，有饥饿情况发生，需要解决。

### 初版 Mutex

```go
// 互斥锁的结构，包含两个字段
type Mutex struct {
    key  int32 // 锁是否被持有的标识
    sema int32 // 信号量专用，用以阻塞/唤醒goroutine
}
```

Unlock 方法可以被任意的 goroutine 调用释放锁，即使是没持有这个互斥锁的 goroutine，也可以进行这个操作。这是因为，Mutex 本身并没有包含持有这把锁的 goroutine 的信息，所以，Unlock 也不会对此进行检查。Mutex 的这个设计一直保持至今。

**在使用 Mutex 的时候，需要严格遵循 “谁申请，谁释放” 原则。**

### 解决饥饿

由于使用了给新人机会，又肯呢个会出现每次都会被新来的 goroutine 获取到锁，导致等待的 goroutine 一直获取不到锁，造成饥饿问题。

![state 字段设计](https://webp.debuginn.com/202302282009302.jpg)

```go
type Mutex struct {
    state int32
    sema  uint32
}

const (
    mutexLocked = 1 << iota // mutex is locked
    mutexWoken
    mutexStarving // 从state字段中分出一个饥饿标记
    mutexWaiterShift = iota

    starvationThresholdNs = 1e6
)

func (m *Mutex) Lock() {
    // Fast path: 幸运之路，一下就获取到了锁
    if atomic.CompareAndSwapInt32(&m.state, 0, mutexLocked) {
        return
    }
    // Slow path：缓慢之路，尝试自旋竞争或饥饿状态下饥饿goroutine竞争
    m.lockSlow()
}

func (m *Mutex) lockSlow() {
    var waitStartTime int64
    starving := false // 此goroutine的饥饿标记
    awoke := false // 唤醒标记
    iter := 0 // 自旋次数
    old := m.state // 当前的锁的状态
    for {
        // 锁是非饥饿状态，锁还没被释放，尝试自旋
        if old&(mutexLocked|mutexStarving) == mutexLocked && runtime_canSpin(iter) {
            if !awoke && old&mutexWoken == 0 && old>>mutexWaiterShift != 0 &&
                atomic.CompareAndSwapInt32(&m.state, old, old|mutexWoken) {
                awoke = true
            }
            runtime_doSpin()
            iter++
            old = m.state // 再次获取锁的状态，之后会检查是否锁被释放了
            continue
        }
        new := old
        if old&mutexStarving == 0 {
            new |= mutexLocked // 非饥饿状态，加锁
        }
        if old&(mutexLocked|mutexStarving) != 0 {
            new += 1 << mutexWaiterShift // waiter数量加1
        }
        if starving && old&mutexLocked != 0 {
            new |= mutexStarving // 设置饥饿状态
        }
        if awoke {
            if new&mutexWoken == 0 {
                throw("sync: inconsistent mutex state")
            }
            new &^= mutexWoken // 新状态清除唤醒标记
        }
        // 成功设置新状态
        if atomic.CompareAndSwapInt32(&m.state, old, new) {
            // 原来锁的状态已释放，并且不是饥饿状态，正常请求到了锁，返回
            if old&(mutexLocked|mutexStarving) == 0 {
                break // locked the mutex with CAS
            }
            // 处理饥饿状态

            // 如果以前就在队列里面，加入到队列头
            queueLifo := waitStartTime != 0
            if waitStartTime == 0 {
                waitStartTime = runtime_nanotime()
            }
            // 阻塞等待
            runtime_SemacquireMutex(&m.sema, queueLifo, 1)
            // 唤醒之后检查锁是否应该处于饥饿状态
            starving = starving || runtime_nanotime()-waitStartTime > starvationThresholdNs
            old = m.state
            // 如果锁已经处于饥饿状态，直接抢到锁，返回
            if old&mutexStarving != 0 {
                if old&(mutexLocked|mutexWoken) != 0 || old>>mutexWaiterShift == 0 {
                    throw("sync: inconsistent mutex state")
                }
                // 有点绕，加锁并且将waiter数减1
                delta := int32(mutexLocked - 1<<mutexWaiterShift)
                if !starving || old>>mutexWaiterShift == 1 {
                    delta -= mutexStarving // 最后一个waiter或者已经不饥饿了，清除饥饿标记
                }
                atomic.AddInt32(&m.state, delta)
                break
            }
            awoke = true
            iter = 0
        } else {
            old = m.state
        }
    }
}

func (m *Mutex) Unlock() {
    // Fast path: drop lock bit.
    new := atomic.AddInt32(&m.state, -mutexLocked)
    if new != 0 {
        m.unlockSlow(new)
    }
}

func (m *Mutex) unlockSlow(new int32) {
    if (new+mutexLocked)&mutexLocked == 0 {
        throw("sync: unlock of unlocked mutex")
    }
    if new&mutexStarving == 0 {
        old := new
        for {
            if old>>mutexWaiterShift == 0 || old&(mutexLocked|mutexWoken|mutexStarving) != 0 {
                return
            }
            new = (old - 1<<mutexWaiterShift) | mutexWoken
            if atomic.CompareAndSwapInt32(&m.state, old, new) {
                runtime_Semrelease(&m.sema, false, 1)
                return
            }
            old = m.state
        }
    } else {
        runtime_Semrelease(&m.sema, true, 1)
    }
}
```

## 思考问题

Q： 目前 Mutex 的 state 字段有几个意义，这几个意义分别是由哪些字段表示的？

A：state 字段一共有四个子字段，前三个 bit 是 mutexLocked（锁标记）、mutexWoken（唤醒标记）、mutexStarving（饥饿标记），剩余 bit 标示 mutexWaiter（等待数量）。

Q： 等待一个 Mutex 的 goroutine 数最大是多少？是否能满足现实的需求？

A：目前的设计来看取决于 state 的类型，目前是 int32，由于3个字节代表了状态，有 536870911，一个 goroutine 初始化的为 2kb，约等于 1024 GB 即 1TB，目前内存体量那么大的服务还是少有的，可以满足现在的使用。


**常见错误的四种场景**

1. Lock/Unlock 不是成对出现;
2. Copy 已使用的 Mutex;
3. 重入;
4. 死锁。