---
title: "首场 Trae Meetup， 国产 AI IDE 有着不一样的思路"
date: 2025-08-17T12:00:00+08:00
keywords: "TRAE,AI,IDE,Meetup"
comments: true
tags: ["TRAE", "AI", "IDE", "Meetup"]
categories: ["debuginn"]
image: "https://static.debuginn.com/20250817gogbTv.JPG"
---

## 序

大家好呀，我是Men小羽，今天报名参加了国产AI 智能编辑器 Trae 的首次线下分享会，今天上午还遇到了一个趣事，一直追番的《凡人修仙传》迎来了韩立的结婴一集，其神识（热度）之强大把哔哩哔哩都整崩了，还把微博也整降级了。本次参加 Meetup，最主要的是想了解 Trae IDE 的发展历程以及后续的开发计划，当然最主要的是 SOLO 模式。

## 天猪 / Trae 发展历程及 SOLO 模式

![天猪](https://static.debuginn.com/20250817UreGjl.JPG)

> 作为 Trae IDE 核心开发者，天猪从系统架构与迭代历程入手，梳理并回顾了 Trae IDE 的发展里程碑，包括 2024 年 Trae 上线、2025 年国内外版本发布，以及月活跃用户突破 100 万的关键节点。同时，他阐述了 AI Coding 的三个阶段（辅助编程、结对编程、自主编程），以及 Trae Agent 的架构演进和 SOLO 模式的协作创新。

![架构迭代](https://static.debuginn.com/20250817f9BfvH.jpg)

目前，大部分的公司云端容器还停留在微服务或者微服务到 Faas 过渡的时期，23 年我还做过 FaaS 相关技术的调研：[FAAS 调研笔记](/p/faas-notes/)，设计思路非常的先进，但是需要大量的技术基础能力的建设与迭代，至今我的工作环境大部分还是在微服务阶段，同时 JDK 1.8 似乎是每一个开发者和公司跳不出的大山。

![Trae](https://static.debuginn.com/20250817o60BKc.jpg)

24 年，随着 Idea 辅助编程插件的火热，市场也如雨后春笋冒了出来，一线大厂都推出了自己的辅助编程软件，同样我司 AI 开发团队也推出了相应的工具，这个阶段更可以理解为智能提示 Plus 版本 + AI 集成环境。

![Trae](https://static.debuginn.com/20250817skVFbk.jpg)

成为坚定的 Trae 重度使用用户是因为 Trae IDE 简洁大方的 UI 设计，不知道大家有没有同感，VSCode 和 Cursor 的 UI 设计实在是有些粗糙，Trae 的设计首先从 UI 上就给我带来简洁如 Idea，简约而不简单的感觉，也是我果断放弃使用 VSCode 的一个重大原因，初期，在使用 AI 模型相关辅助编程中，所有的 IDE，除了 Cursor ，基本上智商都处在一个水平。Trae 作为后起之秀，随着模型理解能力的越来越强大加上 Trae 提示及其适配优化的越来越专业，在使用上也逐步媲美 Cursor。

![1](https://static.debuginn.com/20250817GMxwVr.jpg)

![2](https://static.debuginn.com/20250817P9dbd4.jpg)

![3](https://static.debuginn.com/20250817wN1gD3.jpg)

![SOLO](https://static.debuginn.com/20250817dSOUM1.jpg)

SOLO 模式与 Cursor 和其他众多的 AI IDE 不一样的是，他是从 AI 辅助 IDE，进化成了 AI 主导 IDE，这种设计模式将 Code、Tools、MCP 、浏览器等众多服务变成了为 AI 服务的对象，AI 的角色变成了开发工程师，而开发的角色转变成为了产品经理，通过对话，直接一步到位呈现效果，降低了一个 Idea 转变为产品的成本，但是这种模式也非常考验模型的理解能力与协调能力。

![SOLO](https://static.debuginn.com/20250817rzKZdJ.jpg)

## 江波 / Trae cue 功能设计与挑战

![江波](https://static.debuginn.com/20250817tkglaa.jpg)

> 聚焦 TRAE 的智能编程工具 Cue（Context Understanding Engine），介绍了其核心功能，如代码补全、多点编辑和智能导入。他分析了 Cue 面临的三大挑战：用户意图理解（非线性编辑历史导致的意图偏差）、修改位置确定（兼顾可扩展性与速度）、编辑执行（支持复杂编辑与仓库上下文感知）。此外，他分享了近期优化成果，包括时延从 1s 降至 500ms、融合模型升级，以及支持多语言自动导入，未来有支持基于模型的仓库级跳转预测以更多语言扩展的计划。

![cue](https://static.debuginn.com/202508178hFB0q.jpg)

![cue](https://static.debuginn.com/20250817zKmA9o.jpg)

Cue 功能其实和 Cursor Tab 功能十分的相似，与 Trae SOLO 模式走的是完全相反的两个方向的道路，SOLO 模式目前更像是给轻度技术者快速实现功能的 IDE，而 cue 模式是现在主流 AI IDE 核心增强的能力。

![cue](https://static.debuginn.com/202508178nyTpV.jpg)

cue 功能也面临着 AI 模型版本与理解思维差异的问题，Trae 作为模型整合层，需要不断的适配模型的“个性”来满足用户。

![cue](https://static.debuginn.com/20250817UcamMk.jpg)

cue 功能与 Cursor Tab 功能在我们开发中起到了非常重要的作用，开发效率有了质的提升，但是我认为非常有挑战的就是江波分享的一个点，就是 AI 什么时候该接入帮我们写代码，过渡的介入会引来开发者的反感，克制的介入同样也会引起开发者对于 AI 能力的质疑，这个度该如何平衡？

## 尾

非常有幸报名参加了 Trae 的第一场线下 Meetup，无论是 SOLO 模式还是 cue 功能，对于开发者而言，在我们日常开发中都给我们提供了非常多的便利与效率的提升，Trae IDE 还在高速发展阶段，期待未来可以成为 AI IDE 的顶流产品。

最后，如果你对我的文章及分享感兴趣的话，欢迎你关注我，让我们一起变得更强～