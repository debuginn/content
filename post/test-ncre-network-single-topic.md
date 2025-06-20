---
title: 【NCRE四级网络工程师】计算机网络单选题
date: 2019-02-26T11:58:07+08:00
keywords: "NCRE"
comments: true
tags: ["NCRE"]
categories: ["debuginn"]
image: https://static.debuginn.com/202303261146594.jpg
---

**如果交换机的总带宽为14.4Gbps，它具有12个百兆的全双工端口，则其千兆的全双工端口数量最多为？**

> 全双工交换机的带宽计算方法是：端口数*端口速率*2。 
> 
> 12个百兆的全双工端口占用的带宽为12*2*100=2400 Mbps，则剩余带宽
为14400-2400=12000Mbps。用于千兆的全双工端口，则12000/(2*1000)=6。

**Ethernet网卡使用的物理地址的长度为（48位）。**

**每个物理网络都有自己的MTU，MTU主要规定:一个帧最多能够携带的数据量**

**在客户机/服务器模型中，服务器响应客户机的请求有两种实现方案，它们是并发服务器方案和（重复服务器）。**

**在DNS系统中，如果解析器收到一条“非授权的”服务器响应，那么解析器可以认为（该响应提供的信息可能不准确）。**

**在POP3协议中，查询报文总数和长度可以使用的命令为（STAT）。**

**关于即时通信系统的描述中，正确的是（RFC2778规定了其通讯模型）。**

> 即时通信IM是一种基于Internet的通信服务，由以色列Mirabils公司最早提出，它提供近实时的信息交换和用户状态跟踪。文件
> RFC2778，描述了即时通信系统的功能，正式为即时通信系统勾勒出了模型框架。IM系统一般采用两种通信模式，一种是客户机/服务器模式，另一种采用用户/用户模式，IM软件的文本消息大多使用客户机/服务器模式，而文件传送等大数据量业务使用的是用户/用户模式。在聊天通信中，聊天信息通过加密的方式传输。

**关于即时通信协议的描述中，正确的是（XMPP基于JABBER）。**

> 目前，很多即时通信系统都采用服务提供商自己设计开发的IM协议，如微软MSN采用自己的MSNP协议，AOL采用OSCAR协议，QQ采用自己的私有
协议。目前IM通用的协议主要由两个代表：基于SIP协议框架的SIMPLE协议簇及基于Jabber协议框架的XMPP协议簇。SIP协议称为会话发起协议，它是一种在IP网络上实现实时通信的应用层的控制（信令）协议。

**即时通信系统通常需要支持两种基本的服务，它们是：呈现服务和即时消息服务**

**关于P2P文件共享系统的描述中，错误的是（A）。**

- A) BitTorrent不使用Tracker服务器 
- B) Maze系统含有搜索引擎 
- C) 早期的Napster是一个音乐分享系统 
- D) eDonkey2000采用哈希信息进行文件定位

> BitTorrent协议要求文件的发布者制作一个.torrent文件，被称为“种子”文件，种子文件中包含了Tracker服务器的相关信息和发布者共享的
文件的信息。

**搜索引擎主要由4个关键部分组成，它们是搜索器、检索器、用户接口和（索引器）。**

**利用公钥加密和数字签名技术建立的安全服务基础设施称为（PKI）。**

**关于对称加密的描述中，正确的是（C）。**

- A) 加密密钥与解密密钥不同 
- B) 加密算法与密钥可以公开 
- C) DES属于对称加密方法 
- D) DSA属于对称加密方法

> 对称加密技术使用相同的密钥对信息进行加密和解密。由于通信双方加密与解密使用同一个密钥，所以密钥在加密方和解密方之间的传递和分发必须通过安全通道进行。常用的对称加密算法有DES（数字加密算法）、IDEA算法、RC2算法、RC4算法与Skipjack算法等。

**关于MD5的描述中，错误的是（C）。**

- A) 是一种单向散列函数 
- B) 可用于判断数据完整性 
- C) 属于对称加密方法 
- D) 不能从散列值计算出原始数据

> 散列函数MD5属于一种认证函数，不属于对称加密方法。

**关于P2P文件共享的描述中，正确的是（理论基础是六度分割）。**

> **P2P文件共享的基础是“六度分割”理论。**一般认为P2P文件共享起源于Napster，采用集中式结构，利用
点对点下载过程下载软件，随后另一种P2P文件共享网络Gnutella出现，采用分布式网络共享。BitTorrent即比特洪流，种子文件的扩展名为.torrent。

**在网络管理服务中，定义管理对象结构的是（管理信息库（MIB））。**

> **管理信息库（MIB）是TCP/IP网络管理协议标准框架的内容之一**，MIB定义了受管设备必须保存的数据项、允许对每个数据
项进行的操作及其含义，即管理系统可访问的受管设备的控制和状态信息等数据变量都保存在MIB中。所以在网络管理服务中，定义管理对象结构的是MIB。

**关于CMIP协议的描述中，正确的是（）。**

- A) 由IETF制定 
- B) 针对TCP/IP环境 
- C) 是网络管理的事实标准 
- D) 采用委托监控机制

> 国际标准化组织（ISO）最先在1979年对网络管理通信进行标准化工作，主要针对OSI（开放系统互联）模型而设计。ISO的成果是CMIS和CMIP。
CMIP提供管理信息传输服务的应用层协议，而CMIS支持管理进程和管理代理之间的通信要求，二者规定了OSI系统的网络管理标准。在网络管理过程中，CMIP不是通过轮询而是通过事件报告进行工作的。

**瓦特斯利用电子邮件验证“小世界假设”理论时，邮件平均被转发多少次即可到达接收者手中（6）。**

**IP数据报是IP协议单元使用的数据单元，它的格式可以分为报头区和数据区两大部分，其中数据区包括高层需要传输的数据，而报头区是为了正确传输高层数据而增加的控制信息。**

**在域名系统中，解析器收到一个“非权威性”的映射时，解析器可以认为**（响应服务器不是该域名的授权管理者）。

**如果一个IP数据报的报头长度为256b，那么该数据报报头长度字段的值为（ 8 ）。**

> 头部的IHL域指明了该头部有多长（以32位字的长度为单位），所以256/32=8。