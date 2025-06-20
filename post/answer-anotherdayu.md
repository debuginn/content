---
title: "答 《博客作者呀，我想采访你这 9 个问题！》 问卷"
date: 2024-11-23T13:00:00+08:00
draft: false
keywords: "debuginn,questionnaire"
comments: true
tags: ["debuginn", "questionnaire"]
categories: ["debuginn"]
image: "https://static.debuginn.com/20241123YYRL9F.jpeg"
---

## 简单介绍下自己或者你的博客？

大家好，我是 **Meng小羽**，也是 **Debug客栈** 的博主。这是一个陪伴了我整整八年的博客平台。

最初创办这个网站时，我只是希望用它来记录大学期间的学习笔记，以及算法竞赛中的刷题心得。随着时间推移，博客逐渐成长为一个涵盖多领域的技术分享平台，内容范围也从单一的技术积累拓展到更多元化的话题。

**Debug客栈** 目前已成为一间“全能型的杂货铺”，在这里你可以看到：

- **技术积累与分享**：深入探讨服务端开发、前沿科技等技术内容；
- **科技趣闻与产品体验**：分享数码产品的试用体验与评测；
- **热点讨论**：关注时事，畅谈社会热点与见解；
- **好物与软件推荐**：推荐高效实用的软件和工具，为你的生活和工作增色。

目前，我的职业是 **服务端开发工程师**，专注于系统设计、性能优化以及服务架构相关领域。对于我的更详细介绍，欢迎移步到：[关于站长 - Debug客栈](/about/)。

感谢你关注 Debug客栈，也期待与大家在这里共同成长，探索更多有趣、有价值的内容！

![debuginn](https://static.debuginn.com/20241123LGffwe.png)

## 什么契机让你开始写博客？

我的大学专业是 **计算机科学与技术**，相比其他同龄人，我较早接触到了互联网的思想。从学习编程开始，我遇到不懂的知识点或问题时，总是习惯通过 Google 搜索相关答案和解题思路。在这个过程中，我逐渐接触到了**个人博客**这一领域。

让我印象最深刻的是互联网早期的浪潮，当时人们习惯搭建个人博客，最火的当属新浪博客。而我上大学时，微信公众号迅速崛起，但作为一个计算机相关专业的学生，拥有一个属于自己的主页或站点，不仅能积累知识与技术栈，更是一件非常值得骄傲的事情。

于是，我开始了自己的博客之旅。从第一篇博文 [《Sublime Text：崇高的文本编辑器》](/p/sublime-text/) 开始，一发不可收拾。之后，各种学习笔记和竞赛相关的文章便陆续在博客上发表，为我的知识积累之路画上了一笔又一笔精彩的注脚。

## 你是如何完成创作的？

最初，我的站点使用的是 **WordPress**，通过 **LNMP**（Linux、Nginx、MySQL、PHP）架构进行部署和发布。当时的写作流程很简单：我会先在后台创建好文章标题，根据优先级安排博文的书写，同时反复打磨内容和语言组织。当然，也有不少文章因为各种原因“鸽”了 😄。

到了 2023 年，我的博客完成了 [全站静态化升级](/p/debuginn-hugo-blog/)，采用了 **Hugo** 静态化生成的方式，并部署在 **Cloudflare** 上。写作工具方面，我曾用 **VSCode** 写过一段时间的博文，但总感觉不太适合写作，于是转向了 **Obsidian**。现在，我的流程是：先在 Obsidian 中创建文章标题，完成初稿后再润色一遍，最后复制到 VSCode 进行发布。

此外，博客内容也会同步发布到微信公众号「**Debug客栈**」，欢迎关注！

![obsidian](https://static.debuginn.com/20241123Vio0py.png)

## 运营博客的过程中是否有失去过动力？

> 如果有，是为什么恢复的？如果没有，请问您又是如何保持创作的激情？

在运营博客的这 **8年** 里，我从未想过要放弃。相反，我对折腾充满热情。在这段时间里，我几乎每年都会更换一次博客主题，站点也从动态站点迁移到了静态站点。同时，我始终坚持更新博客文章，虽然 **月更** 对我来说有点难 😅，但 **季更** 还是稳稳地保持着。随着时间的推移，我不断提升自己的写作水平，也逐渐积累了一定的影响力。目前，全网关注人数已接近 **2万**。

对我来说，博客不仅是一个记录的平台，更是展示技术能力与技术影响力的窗口。作为一名互联网从业者，我始终相信博客的价值：它不仅让我系统性地梳理知识，还帮助我结识了许多志同道合的伙伴。在这个过程中，我持续学习，不断成长。

## 如何搭建博客，以及运营博客每年需要投入的资金？

目前，我的博客搭建既简单又省心，但需要具备一定的编程基础。以下是我发布一篇博文的完整流程，与前面提到的创作步骤有些相似：

1. **在 Obsidian 书写草稿**：先完成博文的初步内容；
2. **AI 润色与校对**：使用 AI 工具润色博文，并检查错别字；
3. **上传文章到 GitHub**：博客文章存储在 GitHub 仓库，我通过 VSCode 将文章上传；
4. **替换内部超链接**：在 VSCode 中替换超链接，方便文章内链；
5. **提交代码并合并分支**：将文章推送到 GitHub 的 dev 分支，随后提交合并到 main 分支；
6. **触发 Hugo 编译**：利用 Hugo Actions，将文章生成为静态网页；
7. **部署到 Cloudflare Pages**：通过 Cloudflare Pages 获取编译好的网页，并全球分发；
8. **访问文章**：完成以上步骤后，文章即可通过网站访问。

目前采用的部署方式非常经济高效。借助 **GitHub** 和 **Cloudflare Pages**，博客仓库的部署额度和全球分发额度完全免费。唯一的开销是域名注册费用，每年在 ¥80 左右，可以说是高性价比的博客运营方案。

## 推荐 1 篇你博客中的文章，聊聊原因

今年，我主要围绕两个方向写了一些文章，哈哈，忍不住要推荐给大家：

### 技术领域的总结

这一系列文章聚焦于 **Phoenix 并发框架** 的开发思路，以及我在从 0 到 1 开发框架过程中遇到的问题和解决方案的总结与分享。这个框架已经成功应用到实际业务场景中，非常值得技术爱好者一读！

### RSS 阅读软件推荐

最近，我一直在推广一款开源的 RSS 信息订阅软件 - **Follow**。在我用过的 RSS 软件中，它的交互体验是最友好的，使用起来也非常便捷。我强烈推荐这款工具，带你加入非算法化的信息圈子，掌控属于自己的信息流。

下面我把这两个系列的文章都整理出来，大家可以根据自己的兴趣挑选阅读：

**Phoenix 并发框架系列：**

- [Phoenix框架 从0到1设计业务并发框架 小米商城产品站革新之路](/p/phoenix-framework-1/)
- [Phoenix框架 从0到1设计业务并发框架 怎么组织设计一个框架](/p/phoenix-framework-2/)
- [Phoenix框架 从0到1设计业务并发框架 并发线程池的核心设计](/p/phoenix-framework-3/)
- [Phoenix框架 从0到1设计业务并发框架 自动构建有向无循环图设计](/p/phoenix-framework-4/)

**Follow 分享系列：**

- [Follow｜下一代的信息浏览器](/p/follow-app/)
- [Follow ｜下一代信息浏览器 第二弹来了](/p/follow-app-2/)
- [Follow 给我空投了 1w 代币，可以无限发码啦～](/p/follow-app-airdrop/)
- [2023 年为何我还在使用 RSS](/p/debuginn-2023-rss/)

## 推荐 1 个你喜欢读的博客，聊聊原因

相信许多热爱冲浪、阅读博客的朋友，都或多或少听说过《[阮一峰的网络日志](https://www.ruanyifeng.com/blog/)》。还记得 2019 年实习时，那时候地铁通勤网络状况不太好，我用了一周时间，把阮一峰老师的文章从头到尾读了一遍。这些文章涵盖了他对社会问题的看法、基础技术的分享，以及他每周更新的 **《科技爱好者周刊》** ，让我受益匪浅。

说到周刊，这也是我每周五的必读内容之一。通过阮老师的周刊，我接触到了许多新奇的想法和科技圈的动态，同时还发现了一些由爱好者分享的优质文章和实用软件。5 年下来，这份周刊始终保持着高质量的内容输出，值得推荐给所有感兴趣的朋友。

## 推荐 1 个近期喜欢的事物？

> 例如书籍、电影、音乐、工具、软件。

作为互联网分享者，每个方向都给大家推荐一下吧～

### 书籍

谈到书籍，不得不给大家推荐下马伯庸老师的《太白金星有点烦》，懂得自然懂，哈哈，佛曰不可说不可说，趁着还可以阅读，推荐给大家，通过这本书，大家也可以了解到社会运转的规律。

大家也可以阅读下马老师的另外一本书《长安的荔枝》，看看荔枝使怎么博得贵妃笑的。

### 电影

《楚门的世界》，最经典的莫过于在剧情中和最后楚门的台词：

> Good morning, and in case i don't see you, good afternoon, good evening, and good night!
> 早上好，以防我见不着你，所以下午好，晚上好，晚安！

反观楚门的世界，我们又不是无时无刻也活在“楚门的世界”之中呢？

### 音乐

推荐下最近听的比较多也比较震撼的刀郎老师（罗林）的《如是我闻》音乐专辑，其实是佛教中的《金刚经》，刀郎老师谱曲演唱的，一共有 32 品，推荐给大家聆听。

### 工具

莫过于笔记工具，也就是我现在书写这篇《答博客作者采访问题》博文的工具，Obsidian。

Markdown 友好，可以基于 iCloud 跨设备同步，界面 UI 美观且功能强大。

### 软件

不在多说了，请看楼上 Follow 推荐文章。

## 想做还没有做的事？

谈到还未实现的愿望，我一直想去一趟西藏。2023 年，我曾去过云南，途经香格里拉，深深感受到藏族同胞的淳朴与热情。这段旅程让我更加向往那片神秘的高原，也让我庆幸能生活在拥有“世界第三极”的国家。

如今，阻碍我的似乎只剩下一张通往西藏的“车票”。我希望未来 5 年内，能踏上这片离天空最近的土地，去感受虔诚的信仰，体验雪域高原的热情，感受缺氧中透出的独特幸福感。这是我对雪域之巅的一份憧憬，也是一份内心的向往。

## 写到这里，闭上你的眼睛，深呼吸几分钟，或是出去溜达一圈，然后回来写任何你想写的东西。

Good morning, and in case i don't see you, good afternoon, good evening, and good night!

问卷地址：[博客作者呀，我想采访你这 9 个问题！ - Another Dayu](https://anotherdayu.com/2024/5962/)

最后，感谢你的阅读，你也可以在评论区分享着你的生活与所有，让我们一起变得更强～