---
title: "Go 并发编程之 RWMutex"
date: 2020-12-05T22:17:00+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "go,concurrence,RWMutex"
comments: true
weight: 0
tags: ["go","concurrence","RWMutex"]
categories: ["golang"]
image: "https://webp.debuginn.com/202302281957667.jpg"
---

Mutex 是用来保证只有一个 goroutine 访问共享资源，在大量的并发场景中，特别是读场景中，一个共享资源块只能让 goroutine 串行访问，这就导致了性能的影响，解决方法就是区分读写操作。

这样就可以将串行的读变成并行的读，用来提高读操作的性能。

**Go 标准库 RWMutex （读写锁）就是用来解决 readers-writers 问题的。**

## RWMutex

标准库中的 RWMutex 是一个 reader/writer 互斥锁，**RWMutex 在同一时间只能由 n 个 reader 持有，或者只能被单个的 writer 持有。**

- Lock/Unlock：写操作时调用的方法，若是被 reader 或者 writer 持有， Lock 会一直阻塞，直到可以获取到锁，Ulock 是释放锁； 
- Rlock/RUnlock：读操作时低哦用的方法，如果已经被 writer 持有的话， Rlock 会一直阻塞，直到获取到锁，否者直接返回， RUlock 是 reader 释放锁的方法； 
- RLocker：为读操作返回一个 Locker 接口的对象。

**RWMutex 的零值是未加锁的状态，所以在使用 RWMutex 作为变量或者嵌入到 struct 中去，都没有必要进行显式的初始化。**

## 实现原理

针对于 readers-writers 问题是基于对读和写操作的优先级，读写锁的设计分为三类：

- **Read-preferring 读优先设计**：并发效果好，但是在大量的并发场景下会导致写饥饿；
- **Write-preferring 写优先设计**：针对新请求而言，主要是避免了 writer 饥饿问题，也就是说同一时间有一个 reader 和 writer 等待获取锁，会优先给 writer；
- 不指定优先级：FIFO，不区分读写优先级，适用于某些特定的场景。

**RWMutex 设计是 write-preferring 写优先设计。一个正在阻塞的 Lock 调用会排出新的 reader 请求到锁。**

RWMutex 包含一个 Mutex，以及四个辅助字段 writerSem、readerSem、readerCount 和 readerWait：

```go
type RWMutex struct {
  w           Mutex   // 互斥锁解决多个 writer 的竞争
  writerSem   uint32  // writer 信号量
  readerSem   uint32  // reader 信号量
  readerCount int32   // reader 的数量，记录当前 reader 等待的数量
  readerWait  int32   // writer 等待完成的 reader的 数量
}

const rwmutexMaxReaders = 1 << 30
```

### RLock/RUlock 实现

```go
func (rw *RWMutex) RLock() {
	// 对 reader 计数 +1，readerCount 会出现负数
	// 1、没有 writer 竞争或者持有锁的时候，readerCount 充当计数器存在
	// 2、如果有 writer 竞争锁或者持有锁时，那么，readerCount 不仅仅承担着 reader 的计数功能，还能够标识当前是否有 writer 竞争或持有锁
	if atomic.AddInt32(&rw.readerCount, 1) < 0 {
		// rw.readerCount 是负值的时候，意味着此时有 writer 等待请求锁
		// 因为writer优先级高，所以把后来的 reader 阻塞休眠
		runtime_SemacquireMutex(&rw.readerSem, false, 0)
	}
}

func (rw *RWMutex) RUnlock() {
	// 将 reader 计数 -1
	if r := atomic.AddInt32(&rw.readerCount, -1); r < 0 {
		// 如果为 负数，代表着当前有 writer 在竞争锁，检查是不是所有的 reader 都将锁释放
		// 若释放了就让 writer 获取到锁进行写操作
		rw.rUnlockSlow(r) // 有等待的writer
	}
}

func (rw *RWMutex) rUnlockSlow(r int32) {
	// rUnlockSlow 将持有锁的 reader 计数 -1 的时候；
	// 会检查既有的 reader 是不是都已经释放了锁；
	// 如果都释放了锁，就会唤醒 writer，让 writer 持有锁。
	if atomic.AddInt32(&rw.readerWait, -1) == 0 {
		// 最后一个reader了，writer终于有机会获得锁了
		runtime_Semrelease(&rw.writerSem, false, 1)
	}
}

```

### Lock / Unlock

**RWMutex 是一个多 writer 多 reader 的读写锁，所以同时可能有多个 writer 和 reader。那么，为了避免 writer 之间的竞争，RWMutex 就会使用一个 Mutex 来保证 writer 的互斥。**

```go
func (rw *RWMutex) Lock() {
    // 首先解决其他 writer 竞争问题
    rw.w.Lock()
    // 反转 readerCount，告诉 reader 有 writer 竞争锁
    r := atomic.AddInt32(&rw.readerCount, -rwmutexMaxReaders) + rwmutexMaxReaders
    // 如果当前有 reader 持有锁，那么需要等待
    if r != 0 && atomic.AddInt32(&rw.readerWait, r) != 0 {
        runtime_SemacquireMutex(&rw.writerSem, false, 0)
    }
}
```

一旦一个 writer 获得了内部的互斥锁，就会反转 readerCount 字段，把它从原来的正整数 readerCount(>=0) 修改为负数（readerCount-rwmutexMaxReaders），让这个字段保持两个含义（既保存了 reader 的数量，又表示当前有 writer）。

```go
func (rw *RWMutex) Unlock() {
    // 告诉 reader 没有活跃的 writer 了
    r := atomic.AddInt32(&rw.readerCount, rwmutexMaxReaders)
    
    // 唤醒阻塞的 reader 们
    for i := 0; i < int(r); i++ {
        runtime_Semrelease(&rw.readerSem, false, 0)
    }
    // 释放内部的互斥锁
    rw.w.Unlock()
}
```

当一个 writer 释放锁的时候，它会再次反转 readerCount 字段。可以肯定的是，因为当前锁由 writer 持有，所以，readerCount 字段是反转过的，并且减去了 rwmutexMaxReaders 这个常数，变成了负数。

所以，这里的反转方法就是给它增加 rwmutexMaxReaders 这个常数值。既然 writer 要释放锁了，那么就需要唤醒之后新来的 reader，不必再阻塞它们了，让它们开开心心地继续执行就好了。

在 RWMutex 的 Unlock 返回之前，需要把内部的互斥锁释放。释放完毕后，其他的 writer 才可以继续竞争这把锁。

### RWMutex 常犯的三种错误

- 不可复制 
- 重入导致死锁 
- 释放未加锁的 RWMutex