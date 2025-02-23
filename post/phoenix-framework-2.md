---
title: "Phoenix框架 从0到1设计业务并发框架 怎么组织设计一个框架"
date: 2024-03-18T20:00:00+08:00
keywords: "phoenix,java"
comments: true
tags: ["phoenix","java"]
categories: ["phoenix"]
image: "https://webp.debuginn.com/202402111005028.jpeg"
---

上篇文章主要讲了设计 Phoenix 框架前的遇到的问题和设计框架的思路 [《 Phoenix 框架 从0到1设计业务并发框架 小米商城产品站革新之路》](/p/phoenix-framework-1/)，本篇文章主要讲一下如何设计框架的。

**不死鸟并发框架，是自动构建有向图按照深度进行构建并发组并进行并发调用结果的框架。**

产品站业务静态接口与动态接口都需要调用大量的后台服务进行获取数据进行业务编排，而各个并发调用之间又相互存在依赖，采用并发组设计拆解依赖，同时并发控制调用，BO to DTO 采用统一的 Transfer 层进行设计，开发人员只需要关系定义每次调用事件的 Task 和 Transfer 代码逻辑的书写，直接返回业务数据。

![分层设计](https://webp.debuginn.com/20240308vidGwp.jpeg)

## 名词解释

- **PhoenixFramework** 不死鸟（凤凰）框架，此业务并发框架的名称；
- **Task** 在业务并发中定义一次调用，可以是 HTTP、DUBBO 或者是 Redis 获取、MySQL 读库操作；
- **Transfer** 在业务定义中是一个子业务模块的转换逻辑将 BO 数据转换为 DTO 数据；

## Task 与 Trans 注解

### 怎么定义 Task

在框架设计之初，我们内部有两种方案，一种是继承抽象类实现的方式，Task 通过继承实现 PhoenixTask 类实现定义 Task，另外一种是采用注解的方式，将每个 Task 都定义成具有强约束的 Task ，并且把详细的描述信息在注解中定义，给开发人员一目了然的设计思路。

经过内部讨论，我们选择了 Java 优秀的语言特性，注解的方式声明定义 Task ，这样的定义使得代码简洁明了，也有利于通过 Spring Bean 收集工具来收集我们的定义。

```java
/**  
 * PhoenixTask 
 * 任务注解  
 *  
 * @author debuginn
 */
@Retention(RetentionPolicy.RUNTIME)  
@Target(ElementType.TYPE)  
public @interface PhoenixTask {  
    String taskName();                     // 任务名称  
    String[] beforeTaskName() default {};  // 前置任务  
    String[] filterPlatform() default {};  // 过滤渠道，黑名单  
    String[] taskBoName();                 // 任务生成的 BO 数据 用来校验冲突  
}
```

`PhoenixTask` 注解定义非常简单：

- `taskName`用来标识任务名称，采用枚举，强制约束命名的唯一；
- `beforeTaskName`前置任务，前面讲到每个任务都是一次事件，区分前置任务是需要在并发调用的时候等待结果的返回，之后用来作为此 Task 调用的前置参数；
- `filterPlatform` 过滤渠道，也就是黑名单的功能，但请求渠道在 Task 中声明了黑名单，在并发执行的时候就自动屏蔽掉执行；
- `taskBoName`任务转化为 BO 的数据，通过接口调用或者中间件获取数据，转化为 Transfer 层使用的数据，在框架层做数据参数校验；

### 怎么定义 Trans

Trans 是 Transfer 的简称，同样和 Task 设计一样，也是采用注解的方式定义：

```java
/**  
 * PhoenixTrans 
 * 业务编排注解  
 *  
 * @author debuginn 
 */
@Retention(RetentionPolicy.RUNTIME)  
@Target(ElementType.TYPE)  
public @interface PhoenixTrans {  
    String transName();             // 业务编排块名称  
    String apiName();               // 执行 使用并发 api  
    String[] tasks() default {};    // 依赖的 task 任务  
}
```

`PhoenixTrans` 注解定义同样非常简单：

- `transName`用来标识业务编排块名称；
- `apiName` 用来区分这个 Transfer 业务编排是隶属于哪个并发 API 所属的；
- `tasks`就是定义依赖的 Task 任务有哪些，一个业务编排可能会利用 n 个 Task 返回的 BO 数据进行编排；

> 大家在这里可能会比较疑惑，为啥 Task 中没有定义 apiName 而是 Transfer 中定义的，是因为在设计中，为了便于后续 Task 可以被 n 个并发 API 共·用，这样在 Transfer 定义了 apiName，之后通过 tasks 定义依赖的 Task 就可以推断出这个 Task 目前是被哪一个并发 API 使用的。

## 怎么收集 Task 和 Trans

自定义了 `PhoenixTask` 和 `PhoenixTrans` 注解，通过声明一个 `AnnotationProcessor` 继承 `BeanPostProcessor` 来进行收集定义的注解。

![收集 Task 与 Trans](https://webp.debuginn.com/20240318xHJQIJ.jpeg)

- 首先是根据注解类收集上来对应的 Task 和 Trans；
- 根据不同的 Trans 划分不同的 API，收集不同 API 依赖的 Task；
- 按照 Trans 是否进行依赖过滤使用到的 Task；
- 根据 Task 之间的相互依赖关系，将 Task 进行分组；

![划分 API](https://webp.debuginn.com/20240318m9lRul.jpeg)

![划分并发调用组](https://webp.debuginn.com/20240318Wzvtlt.jpeg)

这样就完成了对框架的分层与自动构建的设计，框架的设计主要的是要思考如何将实际业务中使用的**模块抽象化设计**，同时要思考框架的扩展性与强约束性。

## 结尾

本篇文章主要讲解我如何将业务与调用关系进行抽象成 Trans 与 Task 的，接下来我将讲述并发框架并发线程池的核心设计、配置化思考、监控设计以及自动构建算法等系列文章。

如果你感兴趣，推荐关注公众号或订阅本站，欢迎互动与交流，让我们一起变得更强～

![WeChat](https://webp.debuginn.com/202302202248422.png)