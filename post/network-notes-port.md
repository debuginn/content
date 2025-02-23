---
title: "网络笔记之端口及常见端口号"
date: 2019-06-15T10:59:47+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "计算机网络"
comments: true
weight: 0
tags: ["计算机网络"]
categories: ["debuginn"]
---

## 端口定义

> 通信端口，又称为连接端口、端口、协议端口在计算机网络中是一种经由软件创建的服务，在一个计算机操作系统中扮演通信的端点。每个通信端口都会与主机的IP地址及通信协议关联。通信端口以16比特数字来表示，这被称为通信端口编号。 位于传输层的通信协议通常需要指定端口号，例如在TCP/IP协议族之下的TCP与UDP协议。
引用来源：维基百科

传输层协议，如传输控制协议（TCP）与用户数据包协议（UDP），在分组表头中，定义了来源端口号与目的端口号。

一个通信端口号使用16位无符号整数（unsigned integer）来表示，其范围介于0与65535之间。

在TCP协议中，端口号0是被保留的，不可使用。

- 1--1023 系统保留，只能由root用户使用。
- 1024---4999 由客户端程序自由分配。
- 5000---65535 由服务器端程序自由分配在UDP协议中，来源端口号是可以选择要不要填上，如果设为0，则代表没有来源端口号。

## 常见端口对照表

| 端口号码 / 层 | 	名称            | 	注释                                         |
|----------|----------------|---------------------------------------------|
| 1	       | tcpmux         | 	TCP 端口服务多路复用                               |
| 5	       | rje	           | 远程作业入口                                      |
| 7	       | echo	          | Echo 服务                                     |
| 9	       | discard	       | 用于连接测试的空服务                                  |
| 11	      | systat         | 	用于列举连接了的端口的系统状态                            |
| 13	      | daytime        | 	给请求主机发送日期和时间                               |
| 17       | qotd           | 给连接了的主机发送每日格言                               |
| 18       | msp            | 消息发送协议                                      |
| 19       | chargen        | 字符生成服务；发送无止境的字符流                            |
| 20       | ftp-data       | FTP 数据端口                                    |
| 21       | ftp            | 文件传输协议（FTP）端口；有时被文件服务协议（FSP）使用              |
| 22       | ssh            | 安全 Shell（SSH）服务                             |
| 23       | telnet         | Telnet 服务                                   |
| 25       | smtp           | 简单邮件传输协议（SMTP）                              |
| 37       | time           | 时间协议                                        |
| 39       | rlp            | 资源定位协议                                      |
| 42       | nameserver     | 互联网名称服务                                     |
| 43       | nicname        | WHOIS 目录服务                                  |
| 49       | tacacs         | 用于基于 TCP/IP 验证和访问的终端访问控制器访问控制系统             |
| 50       | re-mail-ck     | 远程邮件检查协议                                    |
| 53       | domain         | 域名服务（如 BIND）                                |
| 63       | whois++        | WHOIS++，被扩展了的 WHOIS 服务                      |
| 67       | bootps         | 引导协议（BOOTP）服务；还被动态主机配置协议（DHCP）服务使用          |
| 68       | bootpc         | Bootstrap（BOOTP）客户；还被动态主机配置协议（DHCP）客户使用     |
| 69       | tftp           | 小文件传输协议（TFTP）                               |
| 70       | gopher         | Gopher 互联网文档搜寻和检索                           |
| 71       | netrjs-1       | 远程作业服务                                      |
| 72       | netrjs-2       | 远程作业服务                                      |
| 73       | netrjs-3       | 远程作业服务                                      |
| 73       | netrjs-4       | 远程作业服务                                      |
| 79       | finger         | 用于用户联系信息的 Finger 服务                         |
| 80       | http           | 用于万维网（WWW）服务的超文本传输协议（HTTP）                  |
| 88       | kerberos       | Kerberos 网络验证系统                             |
| 95       | supdup         | Telnet 协议扩展                                 |
| 101      | hostname       | SRI-NIC 机器上的主机名服务                           |
| 102      | iso-tsap       | ISO 开发环境（ISODE）网络应用                         |
| 105      | csnet-ns       | 邮箱名称服务器；也被 CSO 名称服务器使用                      |
| 107      | rtelnet        | 远程 Telnet                                   |
| 109      | pop2           | 邮局协议版本2                                     |
| 110      | pop3           | 邮局协议版本3                                     |
| 111      | sunrpc         | 用于远程命令执行的远程过程调用（RPC）协议，被网络文件系统（NFS）使用       |
| 113      | auth           | 验证和身份识别协议                                   |
| 115      | sftp           | 安全文件传输协议（SFTP）服务                            |
| 117      | uucp-path      | Unix 到 Unix 复制协议（UUCP）路径服务                  |
| 119      | nntp           | 用于 USENET 讨论系统的网络新闻传输协议（NNTP）               |
| 123      | ntp            | 网络时间协议（NTP）                                 |
| 137      | netbios-ns     | 在红帽企业 Linux 中被 Samba 使用的 NETBIOS 名称服务       |
| 138      | netbios-dgm    | 在红帽企业 Linux 中被 Samba 使用的 NETBIOS 数据报服务      |
| 139      | netbios-ssn    | 在红帽企业 Linux 中被 Samba 使用的NET BIOS 会话服务       |
| 143      | imap           | 互联网消息存取协议（IMAP）                             |
| 161      | snmp           | 简单网络管理协议（SNMP）                              |
| 162      | snmptrap       | SNMP 的陷阱                                    |
| 163      | cmip-man       | 通用管理信息协议（CMIP）                              |
| 164      | cmip-agent     | 通用管理信息协议（CMIP）                              |
| 174      | mailq          | MAILQ                                       |
| 177      | xdmcp          | X 显示管理器控制协议                                 |
| 178      | nextstep       | NeXTStep 窗口服务器                              |
| 179      | bgp            | 边界网络协议                                      |
| 191      | prospero       | Cliffod Neuman 的 Prospero 服务                |
| 194      | irc            | 互联网中继聊天（IRC）                                |
| 199      | smux           | SNMP UNIX 多路复用                              |
| 201      | at-rtmp        | AppleTalk 选路                                |
| 202      | at-nbp         | AppleTalk 名称绑定                              |
| 204      | at-echo        | AppleTalk echo 服务                           |
| 206      | at-zis         | AppleTalk 区块信息                              |
| 209      | qmtp           | 快速邮件传输协议（QMTP）                              |
| 210      | z39.50         | NISO Z39.50 数据库                             |
| 213      | ipx            | 互联网络分组交换协议（IPX），被 Novell Netware 环境常用的数据报协议 |
| 220      | imap3          | 互联网消息存取协议版本3                                |
| 245      | link           | LINK                                        |
| 347      | fatserv        | Fatmen 服务器                                  |
| 363      | rsvp_tunnel    | RSVP 隧道                                     |
| 369      | rpc2portmap    | Coda 文件系统端口映射器                              |
| 370      | codaauth2      | Coda 文件系统验证服务                               |
| 372      | ulistproc      | UNIX Listserv                               |
| 389      | ldap           | 轻型目录存取协议（LDAP）                              |
| 427      | svrloc         | 服务位置协议（SLP）                                 |
| 434      | mobileip-agent | 可移互联网协议（IP）代理                               |
| 435      | mobilip-mn     | 可移互联网协议（IP）管理器                              |
| 443      | https          | 安全超文本传输协议（HTTP）                             |
| 444      | snpp           | 小型网络分页协议                                    |
| 445      | microsoft-ds   | 通过 TCP/IP 的服务器消息块（SMB）                      |
| 464      | kpasswd        | Kerberos 口令和钥匙改换服务                          |
| 468      | photuris       | Photuris 会话钥匙管理协议                           |
| 487      | saft           | 简单不对称文件传输（SAFT）协议                           |
| 488      | gss-http       | 用于 HTTP 的通用安全服务（GSS）                        |
| 496      | pim-rp-disc    | 用于协议独立的多址传播（PIM）服务的会合点发现（RP-DISC）           |
| 500      | isakmp         | 互联网安全关联和钥匙管理协议（ISAKMP）                      |
| 535      | iiop           | 互联网内部对象请求代理协议（IIOP）                         |
| 538      | gdomap         | GNUstep 分布式对象映射器（GDOMAP）                    |
| 546      | dhcpv6-client  | 动态主机配置协议（DHCP）版本6客户                         |
| 547      | dhcpv6-server  | 动态主机配置协议（DHCP）版本6服务                         |
| 554      | rtsp           | 实时流播协议（RTSP）                                |
| 563      | nntps          | 通过安全套接字层的网络新闻传输协议（NNTPS）                    |
| 565      | whoami         | whoami                                      |
| 587      | submission     | 邮件消息提交代理（MSA）                               |
| 610      | npmp-local     | 网络外设管理协议（NPMP）本地 / 分布式排队系统（DQS）             |
| 611      | npmp-gui       | 网络外设管理协议（NPMP）GUI / 分布式排队系统（DQS）            |
| 612      | hmmp-ind       | HMMP 指示 / DQS                               |
| 631      | ipp            | 互联网打印协议（IPP）                                |
| 636      | ldaps          | 通过安全套接字层的轻型目录访问协议（LDAPS）                    |
| 674      | acap           | 应用程序配置存取协议（ACAP）                            |
| 694      | ha-cluster     | 用于带有高可用性的群集的心跳服务                            |
| 749      | kerberos-adm   | Kerberos 版本5（v5）的“kadmin”数据库管理              |
| 750      | kerberos-iv    | Kerberos 版本4（v4）服务                          |
| 765      | webster        | 网络词典                                        |
| 767      | phonebook      | 网络电话簿                                       |
| 873      | rsync          | rsync 文件传输服务                                |
| 992      | telnets        | 通过安全套接字层的 Telnet（TelnetS）                   |
| 993      | imaps          | 通过安全套接字层的互联网消息存取协议（IMAPS）                   |
| 994      | ircs           | 通过安全套接字层的互联网中继聊天（IRCS）                      |
| 995      | pop3s          | 通过安全套接字层的邮局协议版本3（POPS3）                     |