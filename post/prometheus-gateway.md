---
title: "我们是如何用 Prometheus 对网关进行监控的"
date: 2021-12-11T20:00:00+08:00
keywords: "prometheus,grafana,open falcon,counter,histogram"
comments: true
tags: ["prometheus","grafana","open falcon","counter","histogram"]
categories: ["debuginn"]
image: "https://webp.debuginn.com/202302262145770.jpg"
---

近期，我们对 APP 网关 Gateway 做了升级，由于项目创建时间过早（6年前的项目），那时候还没有好的包管理工具，使用的是最原始的 Go Path 来进行项目的依赖管理，历史包袱比较重，项目中很多的第三方引用都是直接将代码拷贝到项目目录下，升级与维护起来特别麻烦，升级之后就是现在官方主推的是 Go module 包管理方式。

解决了上面的这个痛点，网关程序就可以集成一些业界主流的基础工具，升级与维护起来就简单多了。

言归正传，本文主要是讲的我们是如何用 Prometheus 对网关进行监控的，之前我们的网关程序也是集成了我们公司开源打点监控工具 [Open falcon](https://github.com/open-falcon/falcon-plus)，并且使用 Grafana 进行绘图并查看，但是为啥我们不再继续使用了？之后我们为啥拥抱了 Prometheus 生态？还有一些打点、报警、绘图的思考，还有一些我们在使用的过程中出现的问题以及解决方案，一一讲解一下。

## 抛弃 Open falcon 拥抱 Prometheus

在决定使用 Prometheus 之前，我们的 Gateway 使用的是 Open falcon，但是一直存在着一个对于我们而言的痛点，就是作为网关程序，历史维护的路由太多了，接口可用性及接口报错无法聚合报警，也就是我们的监控体系存在着盲区，这个对我们而言来说是最为致命的，那个接口出现了问题会直接导致用户的使用，并且我们使用的那些上游服务出现问题我们也无法及时感知。

使用 Prometheus 最主要的是我们可以通过 PromQL 语法进行正则匹配，实现对某个或多个接口的聚合计算并报警，这样就可以解决我们无法聚合报警的一个痛点。

## 打点、绘图、报警

### 打点 全面、量小

作为业务使用，怎么设计点位，既可以满足报警使用，对每个接口进行各项指标的监控，同时要保证点位数据是可穷举的（避免出现 OOM）和产生数据量比较小。简而言之，就是“**监控要全面、打点数据量要小**”，因为数据量大的话在 Prometheus 拉取指标的时间及周期就不得不设置的过大，这样的后果就是造成图的绘制缓慢甚至超时，同时报警也失去了实效性。 我们网关使用的是 http 协议，可以充分利用 Go 的 `net/http` 特性，使用中间件设计，对请求与返回进行打点，于是我们是这样设计的：

- 对任意一个请求做一个 qps 的打点记录（无任何的业务参与其中）；
- 对单个路由请求进行打点（区分业务状态码）；
- 对单个路由请求进行耗时打点（区分业务状态码）。

> **请求路由** 按照业界通用的设计：`/version/model/action`

以上的场景，仅仅使用[指标类型](https://prometheus.io/docs/concepts/metric_types/)中的两种 Counter（计数器） 和 Histogram（直方图）就可以满足我们打点需求。

### 绘图 清晰、快速

构建一栋房子所需的材料都准备好了，准备建造， building......

点位指标收集到了，接下来就是对点位进行各个维度的拼装，来呈现我们想要的图，这里解答一下为什么我们要把业务状态码打到指标中去，以及我们是如何使用的：我们的系统设计采用业务封装错误码，只要是传输调用链路没有问题，所有的场景都走业务状态码，类似的返回解决如下：

```json
{
    "code": 0,
    "desc": "success",
    "data":{
        "result": "ok"
    }
}
```

- code 为 0，代表当前请求是正常的，返回数据会封装在 data 中；
- code 不为 0，代表着当前请求存在业务上可捕获或者自定义的错误。

作为网关程序，与下游微服务采用相同的接口设计，对我们现在的打点设计也是非常友好的。

> 同样的，有的服务使用的是 Restful API 思想，使用的是 http 标准状态码，那就是 200 代表着成功，非 200 代表着业务或者系统存在错误，当然 5XX 错误可以单独拿出来做可用性或者细化的报警。

**之所以打点记录业务状态码，好处如下：**

1. 对业务状态码打点，可以对某个业务上的特定错误进行捕捉，看图及报警都是非常便捷的； 
2. 不影响对接口可用性进行计算，可以多维度聚合计算可用性（根据业务定义而言）。

当然，打点指标设置的**粒度越小**，对应的点位的**存储大小以及聚合运算**的代价也是成倍的提高的。 铺垫了好久，说一下我们是怎么进行绘图的，在打点的时候讲到使用 Counter、Histogram 进行打点，绘图的时候我们主要从以下三点进行可视化：

- 接口的 qps 看图呈现；
- 接口可用性（Pxx）看图呈现；
- 接口请求PXX 耗时统计 看图呈现。

#### 接口 qps 看图绘图

qps 的点位数据怎么打？就是充分利用中间件的设计，在一个请求 prepare 阶段就将该路由记录并获取进行打点。 使用 [PromQL 语句](https://prometheus.io/docs/prometheus/latest/querying/basics/)就可以实现对对应信息看图的绘制。

```graphql
// 过去1分钟 每秒请求 qps 
// sum  求和函数
// rate 计算范围向量中时间序列的每秒平均增长率
// api_request_alert_counter 指标名称
// service_name 和 subject 都是 label kv参数
sum(rate(api_request_alert_counter{service_name="gateway", subject="total"}[1m])) by (subject)
```

#### 接口可用性看图绘图

接口可用性就是验证当前接口在单位时间内的处理正确的请求数目比上总体的请求数目，在打点的时候也讲到，我们业务代码 0 代表着正确返回，非 0 的代表着存在问题，这样就可以很简单的算出来接口的可用性。

```graphql
// 过去1分钟 每秒接口可用性
// sum  求和函数
// rate 计算范围向量中时间序列的每秒平均增长率
// api_request_cost_status_count 指标名称
// service_name 和 code 都是 label kv参数
(sum(rate(api_request_cost_status_count{service_name="gateway", code="0"}[1m])) by (handler) 
/ 
(
sum(rate(api_request_cost_status_count{service_name="gateway", code="0"}[1m])) by (handler) 
+ 
sum(rate(api_request_cost_status_count{service_name="gateway", code!="0"}[1m])) by (handler))
) * 100.0
```

#### 接口 Pxx 耗时统计看图绘图

接口耗时统计打点依赖 prometheus api 中的 histogram 实现，在呈现打点耗时的时候有时候局部的某个耗时过长并不能进行直接反应整体的，我们只需要关注 SLO （服务级别目标）目标下是否达标即可。

```graphql
// 过去1分钟 95% 请求最大耗时统计
// histogram_quantile
1000* histogram_quantile(0.95, sum(rate(api_request_cost_status_bucket{service_name="gateway",handler=~"v1.app.+"}[1m]))
by (handler, le))
```

> histogram_quantile(φ float, b instant-vector) 从 bucket 类型的向量 b 中计算 φ (0 ≤ φ ≤ 1) 分位数（百分位数的一般形式）的样本的最大值。（有关 φ 分位数的详细说明以及直方图指标类型的使用，请参阅直方图和摘要）。向量 b 中的样本是每个 bucket 的采样点数量。每个样本的 labels 中必须要有 le 这个 label 来表示每个 bucket 的上边界，没有 le 标签的样本会被忽略。直方图指标类型自动提供带有 _bucket 后缀和相应标签的时间序列。

上面是官方对于 [histogram_quantile](https://prometheus.io/docs/prometheus/latest/querying/functions/#histogram_quantile) 函数的解释，关注的是 设置 φ 分位数 对应的 bucket 桶，但是实际中有 分位数计算误差的问题。 Prometheus 官方 histogram 设置的默认 buckets 如下：

```go
DefBuckets = []float64{.005, .01, .025, .05, .1, .25, .5, 1, 2.5, 5, 10}
```

这里可以看到我们的接口指标分界时间，每一个请求的耗时都会根据具体的设置的 bucket 的范围落到不同的区间内，这里设置的桶的范围直接影响到计算值的准确度（上面所提到的 分位数计算误差问题）。

### 报警 及时、准确

使用 Prometheus 的 Alert Manager 就可以对服务进行报警，但是如何及时又准确的报警，已经如何合理设置报警，我们就要引入 SLO 的概念，在实际的业务场景中，我们会发现某个接口某个时间段的耗时是一组离散的点：

![请求时间数量分布图](https://webp.debuginn.com/202302262154973.png)

我们可以看到大部分的请求可以在 1s 之内就可以快速的返回，只有个别的请求可能由于网络的抖动、应用短暂升级或者其他因素导致过慢，若是我们直接设置接口最大请求耗时超过2s（持续一个时间段），那我们就面临着疯狂的告警轰炸，同时告警也就失去了针对某个接口的异常活动做出提示供开发人员处理的意义。

> **服务级别目标**（Service-level objective，SLO）是指服务提供者向客户作出的服务保证的量化指标。服务级别目标与服务级别协议有所不同。服务级别协议是指服务提供者向客户保证会提供什么样的服务，服务级别目标则是服务的量化说明。
> [Service-level objective](https://zh.wikipedia.org/wiki/%E6%9C%8D%E5%8A%A1%E7%BA%A7%E5%88%AB%E7%9B%AE%E6%A0%87) 服务级别目标

![请求时间数量分布图](https://webp.debuginn.com/202302262155955.png)

比方说我们发现上面的 90% 请求都在 1s 内返回，我们就可以只需要对 90% 请求耗时做监控分析其调用链路并告警。 举个栗子，比方说我们一个首页的接口 `/v1/home/page` 99% 的请求可以在 500ms 内返回，只有个别的请求超过 2s+ 的时间，大多数情况下我们就不会关心这 1%的请求，那我们就可以定制一个 **持续 1分钟首页 99% 请求耗时大于 1s**的报警，这样当我们收到报警的时候，我们就可以第一时间知道首页出现了问题，我们就可以根据报警及时处理。

**业务的报警是与接口的实现与调用链路的复杂度是紧密结合在一起的，根据不同的业务场景，配置合理的报警才满足我们及时准确的要求。**反之就是配置过高不灵敏、往往线上已经出现了好久报警就是没有，配置过低，分分钟触发报警，对业务开发人员增加了排查问题的时间成本。

## 遇到的问题

### 收集指标过大拉取超时

由于我们是 gateway BFF 层做得指标，本身的路由的基数就比较大，热点路由就有好几百个，再算上对路由的打点、耗时、错误码等等的打点，导致我们每台机器的指标数量都比较庞大，最终指标汇总的时候下游的 prometheus 节点拉取经常出现耗时问题。

前期解决方案比较粗暴，就是修改 prometheus job 的拉取频率及其超时时间，这样可以解决超时问题，但是带来的结果就是最后通过 grafana 看板进行看图包括报警收集上来的点位数据延迟大，并且随着我们指标的设置越来越多的话必然会出现超时问题。

目前的解决方案就是做分布式，采用 prometheus [联邦集群](https://prometheus.io/docs/prometheus/latest/federation/)的方式来解决指标收集过大的问题，采用了分布式，就可以将机器分组收集汇总，之后就可以成倍速的缩小 prometheus 拉取的压力。

![联邦集群设计](https://webp.debuginn.com/202302262157871.png)

### 动态收集机器指标

因为我们机器都是部署在集群上并且会随着活动大促动态调整机器的数量，联邦集群中配置文件最重要的就是配置各个收集节点指标的 IP:Port ，我们不可能每次都去手动维护这个配置，成本比较高，那么我们就需要将配置动态写入，针对此问题，在 leader 的建议下，使用运维服务树拿到该节点下的机器的 Ip，使用脚本程序动态维护起来就非常方便了，默认 Prometheus 是 20s 读取一次配置。

### 请求的耗时看图与报警不准确

这个问题是在我们的业务中，请求耗时最常见的是在 2s 之内返回，但是通过 Prometheus histogram 对应 1-2s 的请求会落在 le 为 2.5 桶中，导致报警误报，我们看日志中的请求在 1.* s 的都算在 2.5 的桶上，而报警的配置是 大于 2s， emmm

```go
DefBuckets = []float64{.005, .01, .025, .05, .1, .25, .5, 1, 2.5, 5, 10}
```

之后根据我们的业务场景调整了一下，使用了自己的 CustomBuckets：

```go
CustomBuckets = []float64{.01, .025, .05, .1, .25, .5, 1, 1.5, 2, 3, 4, 8}
```

## References

1. [Prometheus 官方文档](https://prometheus.io/docs/)
2. [Prometheus 翻译文档](https://prometheus.fuckcloudnative.io/)
3. [wiki SLO 服务级别目标](https://zh.wikipedia.org/wiki/%E6%9C%8D%E5%8A%A1%E7%BA%A7%E5%88%AB%E7%9B%AE%E6%A0%87)
4. [wiki 累积直方图](https://zh.wikipedia.org/wiki/%E7%9B%B4%E6%96%B9%E5%9B%BE%E5%9D%87%E8%A1%A1%E5%8C%96)
