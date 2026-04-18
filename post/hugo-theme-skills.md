---
title: "开源了一个 Hugo 主题，专门用来展示 AI Skill"
date: 2026-04-18T21:19:00+08:00
draft: false
keywords: "Hugo,AI,Skill,开源,主题,Hermes,工具"
comments: true
tags: ["AI","Hugo","开源","Skill"]
categories: ["AI"]
image: "https://webp.debuginn.com/20260418dE4G1Y.png"
---

大家好呀，我是 Meng小羽。

最近一直在折腾 AI 工作流，用 Hermes 这类 AI 助手越用越深，手里的 skill 攒了一大堆——说白了就是一堆"遇到这类问题该怎么做"的操作手册。写着写着就发现一个问题：这些 skill 全躺在本地吃灰，想找个地方展示出来，搜了一圈也没找到合适的现成方案。

行吧，那就自己做一个。

于是就有了 **hugo-theme-skills**，一个专门用来做 skill 和工具介绍页的 Hugo 主题，现在已经开源了。

如果用一句话概括它，我会说：这不是单纯的主题皮肤，而是一个「技能卡片 + 可交互工具页 + 可扩展控件系统」三合一的小框架。

如果你手里已经有一批 AI skill、工作流脚本或者小工具，想把它们整理成一个能展示、能安装、还能直接互动体验的站点，这个主题会比较适合你。



## 选择 Hugo 的原因

静态站点省心这件事不用多说了，Hugo 构建速度快，部署往 GitHub Pages 或者 Cloudflare Pages 一扔就完事，不用操心服务器，也不用担心哪天数据库挂了。这个主题还特意做到了零 Node.js 依赖，不需要什么构建流水线，纯 Hugo + 原生 JS 和 CSS，拉下来就能跑。

不过选 Hugo 还有个更深的原因，这个理由跟 AI 本身有关。

Hugo 用 TOML/YAML 做配置，用 Markdown 写内容，所有结构都是格式清晰、约束明确的。说白了，AI 读你的内容不会有歧义，生成或者修改内容的准确度非常高。如果你手里有一批 skill 想批量生成页面，用 Hugo 做载体简直是天作之合。Hugo 不只是发布工具，它本身就是 AI 工作流里的天然接口。



## 效果展示

### 首页

![首页](https://webp.debuginn.com/20260418kl6pGt.jpg)

### Skill Install

![Skill](https://webp.debuginn.com/20260418YA9wI1.jpg)

### 暗黑模式

![模式](https://webp.debuginn.com/20260418TRRDh2.jpg)

### 工具页面

![Tools](https://webp.debuginn.com/202604185Xtt4k.jpg)

### 多语言

![多语言](https://webp.debuginn.com/20260418HMMmlo.jpg)



## 快速上手

环境要求：Hugo >= 0.115.0（建议 extended 版本），Go >= 1.21。

推荐 Hugo Module 方式接入，三步搞定：

```bash
# 第一步：初始化（已经初始化过的跳过）
hugo mod init github.com/yourname/your-site

# 第二步：在 hugo.toml 里引入主题
[module]
  [[module.imports]]
    path = "github.com/debuginn/hugo-theme-skills"

# 第三步：拉取主题
hugo mod get github.com/debuginn/hugo-theme-skills
hugo mod tidy
```

不想用 Module 的话，git submodule 也行，然后 `hugo.toml` 加 `theme = "hugo-theme-skills"`。

本地跑一下：`cd exampleSite && hugo server`



## 最后

说实话做这个主题的过程还挺愉快的，因为本来就是给自己用的，每加一个功能都能立刻用上，这种"写完就跑"的开发节奏比赶需求舒服多了。

如果你也在用 Hermes、Claude 这类 AI 助手，手里也攒了一批自己的 skill，希望有个好看的地方把它们展示出来，不妨试试这个主题。说不定搭着搭着，就搭出了一个属于自己的小 skill 市场。

- [Demo](https://skills.debuginn.com/)
- [我的工具站点](https://tools.debuginn.com/)
- [GitHub](https://github.com/debuginn/hugo-theme-skills)

欢迎 Star，欢迎提 Issue，也欢迎直接 PR 进来一起玩～
