---
title: "Phoenix框架 从0到1设计业务并发框架 并发线程池的核心设计"
date: 2024-04-07T20:00:00+08:00
keywords: "phoenix,java"
comments: true
tags: ["phoenix","java"]
categories: ["phoenix"]
image: "https://webp.debuginn.com/202402111005028.jpeg"
---

## 背景

从 0 到 1 设计业务并发框架系列：
- [Phoenix 框架 小米商城产品站革新之路](/p/phoenix-framework-1/)
- [Phoenix 框架 怎么组织设计一个框架](/p/phoenix-framework-2/)

前两篇文章已经讲述了我设计框架的背景以及抽象设计的细节，今天讲一下并发框架最为关键的并发线程池的核心设计，主要讲一下在设计线程池划分遇到的问题以及最终我采用了哪种方式实现的。

![并发调用组](https://webp.debuginn.com/202306292017666.png)

将存在依赖关系的 Task 进行划分分组后，依次执行分组就可以拿到所有想要的结果，但是怎么划分线程池、设置线程池是面临的问题。

接下来，我将实际业务中的复杂度简化设计，将问题具象化呈现给大家。

## 方案：公用线程池

### 方案

最开始，我计划将分配的 Task 公用一个线程池，让 Task 去线程池竞争资源，如下图：

![公用线程池](https://webp.debuginn.com/20240402UDmgNy.jpeg)

但是很快发现，单个线程池一旦请求数量上来，某个 Task 接口变慢就会导致整个接口成功率急速下降，直至不可用的状态。

为什么会出现这种情况呢？

### 效果

![公用线程池](https://webp.debuginn.com/202404024RVJfT.jpeg)

- **T1 时刻**，第 1 波流量进来，之后率先执行 TaskA 或者 TaskB；
- TaskA 请求的快速递增，接口变得越来越慢；
- **T2 时刻**，还有两个 TaskA 并没有执行完毕，之后第二波流量进来：
	- 第 1 波流量开始执行 TaskC 和 TaskD；
	- 第 2 波流量进来，也有 TaskA 和 TaskB 获取到线程执行；
- **T3 时刻**，此时已经有 4 个 TaskA 还没有执行完，并且最开始的两个 TaskA 要面临着超时情况：
	- 第 1 波流量执行的 TaskA 面临超时中断的情况；
	- 第 2 波流量执行的 TaskA 也在运行状态中；
	- 第 3 波流量进来，情况变得复杂，新的流量，有 TaskA 和 TaskB 进行执行；
	- 此时第 1 波流量前两层运行完毕，开始执行 TaskE；
	- 此时第 2 波流量的前一层运行完毕，开始执行 TaskC 和 TaskD；
- 之后按照 TaskA 始终慢的情况继续发展.......
- **Tn 时刻**，此时线程池大部分已经被前 n 波流量的 TaskA 占据着，并且大量被中断超时，其他 Task 无法竞争到线程进行执行。

这样的话，接口的可用性完全取决于 TaskA 的可用性，但是还有一个致命的问题就是其他 Task 无法执行或者由于依赖问题，前置该获取用作请求参数大部分为空，也无法正常请求，这样就算是接口返回了数据，也是不全的数据。

这种方案存在**共用线程池大量线程等待超时**的情况，是不可取的。

## 方案：分层线程池

### 方案

公用线程池的情况肯定是有问题的，在此基础上，尝试将分层并发划分不同的并发池，每一层公用线程池，如下图：

![分层线程池](https://webp.debuginn.com/20240402liTZYD.jpeg)

上了分层公用线程池之后，压力测试发现效果只有小幅的提升，没有达到预期的目标，甚至来说相差很远，为啥会出现这个问题？

### 效果

![分层线程池](https://webp.debuginn.com/20240402tpIWJo.jpeg)


我们还是假设 TaskA 会随着请求量上来会大面积超时来举例。

- **T1 时刻**，第 1 波流量进来，之后率先执行 TaskA 或者 TaskB，此次线程池 2、3 没有执行到；
- TaskA 请求的快速递增，接口变得越来越慢；
- **T2 时刻**，还有两个 TaskA 并没有执行完毕，之后第二波流量进来：
	- 第 1 波流量开始执行线程池 2 的线程 TaskC 和 TaskD；
	- 第 1 波流量存在 TaskC 执行完，陆续开始执行线程池 3 的线程 TaskE；
	- 第 2 波流量进来，也有 TaskA 和 TaskB 获取到线程执行；
- **T3 时刻**，此时已经有 4 个 TaskA 还没有执行完，并且最开始的两个 TaskA 要面临着超时情况：
	- 第 1 波流量执行的线程池 1 TaskA 面临超时中断的情况；
	- 第 2 波流量执行的线程池 1 TaskA 也在运行状态中；
	- 第 3 波流量进来，情况变得相对来说比较复杂，新的流量；
	- 此时第 1 波流量前两层运行完毕，开始执行线程池 3  TaskE；
	- 此时第 2 波流量的前一层运行完毕，开始执行线程池 2 TaskC 和 TaskD；
- 之后按照 TaskA 始终慢的情况继续发展.......
- **Tn 时刻**，此时线程池  1 大部分已经被前 n 波流量的 TaskA 占据着，并且大量被中断超时，由于依赖于 TaskA 和 TaskB 的结果作为下层的入参数：
	- TaskA 过慢占据着接近 100% 的线程池 1 的资源；
	- TaskB 竞争不到资源，被超时中断；

最后接口还是发展到不可用的状态，其实和公用线程池的问题一样，也还是存在**大量线程等待超时**
的情况。

这种公用线程池的现状是不可取的，那么该如何划分线程池来执行呢？其实分而治之的思想就可以解决这个问题，也就带来了 3.0 版本，**独立 Task 线程池的方案**。

## 方案：独立线程池

无论怎么公用线程池，都会出现被挤占的情况，只有将每个 Task 划分单独的线程池，才不会出现抢占等待的问题，那么如何设计的呢？

### 方案

![独立线程池](https://webp.debuginn.com/20240402lXnyb4.jpeg)


每个 Task 单独创建线程池来承接流量，各个线程池互相不干扰，同时承接流量交给 CPU 抢占资源进行调度运行。

### 效果

![独立线程池](https://webp.debuginn.com/20240402i6w2aW.jpeg)


由于是单独承接流量，这种设计满足了高可用的目标，还是依照 TaskA 接口随着并发请求的提升，接口越来越慢直至不可用，之后再加入一个条件，就是 **TaskC 的执行条件是 TaskA 执行完毕的结果**。

- **T1 时刻**，第一波流量进来，所有线程池的线程都占满，开始进入核心调度执行；
- **T2 时刻**，第二波请求进来，第一波请求的 2 个 TaskA  还没有执行完毕，其他线程池的线程逐渐承接第二波请求等待调度；
- **T3 时刻**，第三波请求进来，这时候情况比较复杂：
	- 第一波流量的 2 个 TaskA 已经超时被中断了，对应的 TaskC 的线程池的两个 TaskC 线程等待 Task 的执行结果失败，结束任务；
	- 第二波流量的 2 个 TaskA 还没有执行完毕，也濒临超时；
	- 其他线程池执行均正常运行；
- 就这样过了一段时间 ...
- **Tn 时刻**：
	- TaskA 已经达到了不可用的状态；
	- 对此有依赖关系的 TaskC 也逐渐达到不可用状态；
	- 其他线程执行正常；

这么看，针对于一个接口调用几十个上百个接口的场景，不会因为一个接口或者有依赖关系的接口可用性降低而影响整个接口的可用性，同时只要对单个线程池做好监控，加上报警即可动态感知哪些上游接口失败而及时通知到对应的系统维护同学，这样就大大的降低了维护成本。

这个版本作为线上生产环境的第一个版本推了上去，单台 8C 8G (k8s) 的配置空跑框架达到了 QPS 在 1.4w，接口可用性在 99.96%（结果仅供参考，根据公司集群部署策略、机器性能等问题会有浮动）。

但是，这种目前还是存在着显而易见的问题，就是每个 Task 执行的接口的接口响应都不是一致的，有的在 50ms 内、有的在 100ms 内、有的比较慢 500ms 内，分配相同的线程池数量是不合理的，因为这样就会造成 CPU 调度不公平，那么怎么让调度运行的比较公平呢？

### 优化

针对于这个问题，将线程池大小按照**权重**创建，像是比较慢的接口但是多等待一定时间可以返回的，我们就多分配线程池大小，接口响应很快的，我们就相对减少线程池大小，这样的设计可以在保证接口的可用性兼顾接口返回字段的完整性。

## 写在最后

本篇文章主要讲框架设计中怎么将划分好的分层并发执行，最终我们采用了独立线程池的方案，并且按照耗时、CPU 核数等权重评估分配每个 Task 任务线程池的大小，让 CPU 线程调度来确保线程都尽可能的公平执行到，最终保证接口的并发需求及高可用的场景。

如果你感兴趣，推荐关注公众号或订阅本站，欢迎互动与交流，让我们一起变得更强～

![WeChat](https://webp.debuginn.com/202302202248422.png)




