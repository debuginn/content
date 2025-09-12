---
title: "如何接手并维护一个项目"
draft: false
date: 2023-07-10T23:11:00+08:00
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "project"
comments: true
weight: 0
tags: ["project"]
categories: ["debuginn"]
image: "https://webp.debuginn.com/202307102310436.jpg"
---

> 在工作中，接手负责管理别人开发或者前人开发的项目是每个开发人员的工作任务之一，那么，如何快速并且高效的消化接手过来的项目呢，本文主要讲解一些方法与实践技巧，希望可以帮助你快速了解你接手的项目。

## 系统文档

若是有最开始的包括后续优化的相关技术文档或者系统文档，对于接手过来的项目无疑是最有助于开发人员的方式。但是大家会发现往往接手过来的项目是没有这一类的文档的，交接过来的系统若是对开发有极高追求的，一般都会有文档，并且 README.md 中会有项目介绍包括相关文档，但是...... 往往我们拿到手的系统是纯代码，README.md 可能都没有这个文件，这种往往是最痛苦的，不过也是最锻炼梳理系统这项技能的。

那么我们就需要从下面这几个点来慢慢消化系统。

## 系统权限

交接过来的系统，一定要开好对应的权限，这对你全面了解系统以及后续维护系统有着至关重要的的作用，若没有权限，当系统出现问题时，领导找到你问问题原因，而你却在向领导申请权限，世纪名场面。

以下是常见的系统权限：

- Gitlab 仓库权限；
- Deploy 部署系统权限；
- Log    日志系统权限；
- Data   数据库管理系统权限；
- Alert  系统报警配置权限；
- Crond  任务调度器权限；
- Middleware 根据不同系统的中间件权限，包括不限于 Notify、RocketMQ、Redis 等；
- Auth   依赖系统授权信息权限；
- Test   测试平台的权限；
- API    API 调试平台的权限；
- Sys    系统相关注册管理权限；

**接手项目，开启权限是第一步也是必须要做的事情。**

## 配置文件

通过配置文件可以看到一个系统的基本信息，比如说：

- 系统环境配置信息、注册信息、协议相关信息；
- 系统使用数据库配置信息；
- 系统使用 Redis 等中间件配置信息；
- 业务上使用的一些值定义；

## 库表设计

一般数据库表设计会存放在 dao 模块或者目录下，基本上是一表一文件定义，可以看到表定义的字段，并且可以看到对该表的一些“增删查改”动作。

若是底层系统设计，本身系统就是只提供给外部服务使用的，那么从数据库库表设计基本上就可以反推业务逻辑的设计，删除、更新、新增都是基本的业务逻辑动作，查询或者组合成事务的业务相对来说比较复杂，不过根据业务代码看着理解的话也比较简单一些。

## 缓存设计

缓存一般有两种，分别是：

- **被动缓存**：一般是用于高并发场景，用于缓解下游中间件或者接口的瞬时压力；
- **主动缓存**：这种是相对高级的缓存策略，用于分布式数据一致数据的返回；

分析刚接手的系统，从 cacheKey 就可以了解业务系统中为什么这样设计的：

```shell
product.info.pid.XXXX
```

上面这个例子可以看到记录的是一个产品 pid 为 XXXX 的缓存信息。


## 协议文件

若是接手过来的系统按照语义命名及划分路由的话，则通过 API 接口文档来看是一个很好的方式，因为通过 API 基本可以确定接手过来的服务有哪些业务，针对不同的业务又有哪些操作。

针对不同的语言、不同的协议，也有一些细微的差别：

### Java Dubbo 协议

一般 Java Dubbo 协议都是对外提供 API 模块的 pom 依赖的，声明都是使用接口来实现的：

```java
// XXXX 业务模块
public interface XXXX {
    // 获取列表
    Result<DataA> getXXXList(Context ctx, XXRequest req);
    // 获取列表 基于 uid 
    Result<DataA> getXXXListByUid(Context ctx, XXRequest req);
    // 基于 uid 删除 XX 信息
    Result<Boolean> delXXInfoByUid(Context ctx, XXRequest req);
}
```

### Go Thrift 协议

Go Thrift 是使用 [IDL](https://thrift.apache.org/docs/idl) 语言定义的协议，我们会基于 IDL 声明的接口，定义好接口出入参生成的 SDK 文件，通过看 IDL 定义的接口，就可以了解到接手的项目提供了哪些功能了：

```thrift
struct XXOrderResponse {
    1:i64 orderId,
    2:string orderInfo;
}

struct XXOrderRequest {
    1:i64 orderId (go.tag = "json:\"order_id\""),
}

// 接口定义
service OrderService {
    // XX 订单
	XXOrderResponse XXOrderInfo(1:XXOrderRequest params)
}
```

### Go HTTP 协议

目前公司使用居多的 HTTP 框架是 iris 还有一个自研的 migo 框架（基于 beego 框架开发），都有一个相似的特点，就是会把 router 单独定一个文件，即 router.go :

```go
// Init _
func Init(app *iris.Application) {

	app.Use(middlewares.RecoveryMiddle)
	app.Use(middlewares.PromeMiddle)
	app.Use(middlewares.LoggerMiddle())      // 小心打日志的配置
	app.Use(innermiddle.CrossOrigin)         // 是否使用跨域
	app.Use(middlewares.RateLimitMiddleWare) // 配置限流

	app.PartyFunc("/product", cproduct.Register)

	return
}
```

## 编程语言

从依赖方去挖掘系统，看系统依赖了哪些业务方，也可以看出系统依赖了哪些基础包，还可以看到依赖的包对应版本（有经验的人可以看出是否有 bug ）。

### Java Maven

目前 Java 主流的依赖方式就是使用 Maven POM 定义依赖并管理依赖：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>product</artifactId>
        <groupId>cn.debuginn.blog</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>

    <modelVersion>4.0.0</modelVersion>
    <artifactId>product-server</artifactId>

    <dependencies>
        <dependency>
            <groupId>cn.debuginn.blog</groupId>
            <artifactId>product-api</artifactId>
            <version>${product-api-version}</version>
        </dependency>
        <!--单元测试-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>
    </dependencies>
</project>
```

### Go Module

目前 Go 语言也是大一统到 go mod 管理版本依赖了，由于 Go 是源码依赖，使用 `go mod tidy` 之后就可以通过点击依赖仓库来看源码了：

```go
module github.com/debuginn/go-demo

go 1.20

require (
	github.com/dubbogo/gost v1.11.25 // indirect
	github.com/go-playground/validator/v10 v10.10.1
	github.com/go-redis/redis/v8 v8.11.0
	github.com/gobwas/ws v1.0.3 // indirect
)
```

## 流程图、泳道图

通过上述方式与方法，最后就可以根据业务代码，画出业务流程图及对应的泳道了，同时，也可以基于依赖关系，画出系统的架构图，梳理一个系统的最好方式就是有所沉淀，通过读代码把代码转化为文档的方式即可以对消化的系统有所输出，又可以提升自己的技术能力。

## 写在最后

最后，**一个好的系统，是由系统设计文档、代码、注释和覆盖率尽可能全面的测试用例组成的**，很多情况下大家由于不可抗拒的原因，最后只留下了运行的代码，这也是撰写本文的主要原因，希望对大家有所帮助，最后大家有什么好的方式和方法也可以在评论区分享出来，让我们一起变得更强。