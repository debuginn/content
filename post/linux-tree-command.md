---
title: "Linux Tree 树状目录显示工具 使用手册"
date: 2022-03-15T20:08:56+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "linux,unix,tree"
comments: true
weight: 0
tags: ["linux","unix","tree"]
categories: ["OS"]
image: "https://webp.debuginn.com/202303152010224.jpg"
---

Tree 命令以树状形状列出目录的内容的一个工具，你时常在 Github 中常看到一些开源项目会将自己的项目目录展现出来，这篇文章的背景图就是展现的开源项目 Laravel 中 app 目录的树状图，接下来介绍一下基本使用语法。

## 基本语法

```shell
tree [-aACdDfFgilnNpqstux][-I <范本样式>][-P <范本样式>][目录...]
```

## 常用命令

```shell
tree --help     显示帮助信息
tree -d         只显示目录
tree -L n       只显示第n层目录
tree -l         遵循像目录这样的符号链接
tree -f         打印每个文件的完整路径前缀
tree -x         只保留在当前文件系统上
tree -L         级下降深层级目录
tree -R         达到最大等级时重新运行树
tree -P         模式只列出符合给定模式的文件
tree -I         模式不要列出与给定模式匹配的文件
tree -o         文件名输出到文件而不是标准输出
```

## 基本命令

```shell
[➜  ~ tree --help
usage: tree [-acdfghilnpqrstuvxACDFJQNSUX] [-H baseHREF] [-T title ]
	[-L level [-R]] [-P pattern] [-I pattern] [-o filename] [--version]
	[--help] [--inodes] [--device] [--noreport] [--nolinks] [--dirsfirst]
	[--charset charset] [--filelimit[=]#] [--si] [--timefmt[=]<f>]
	[--sort[=]<name>] [--matchdirs] [--ignore-case] [--fromfile] [--]
[<目录列表>]
------- 上市选项 -------
-a            列出所有文件。
-d            仅列出目录。
-l            跟随目录等符号链接。
-f            打印每个文件的完整路径前缀。
-x            仅保留在当前文件系统上。
-L            级别仅下降级别级别的目录。
-R            当达到最大目录级别时，重新运行树。
-P            模式仅列出与给定模式匹配的那些文件。
-I            模式不列出与给定模式匹配的文件。
--ignore-case 模式匹配时忽略大小写。
--matchdirs   在-P模式匹配中包括目录名称。
--noreport    在树列表的末尾关闭文件/目录计数。
--charset X   将charset X用于终端/ HTML和缩进线输出。
--filelimit＃ 不要使包含超过＃个文件的dirs下降。
--timefmt     <f>根据<f>格式打印和格式化时间。
-o filename   输出到文件而不是stdout。

------- 文件选项 -------
-q       将不可打印的字符打印为'？'。
-N       按原样打印不可打印的字符。
-Q       引用双引号的文件名。
-p       打印每个文件的保护。
-u       显示文件所有者或UID号。
-g       显示文件组所有者或GID号。
-s       打印每个文件的大小（以字节为单位）。
-h       以更易于理解的方式打印尺寸。
--si     与-h类似，但以SI单位使用（1000的幂）。
-D       打印上次修改或（-c）状态更改的日期。
-F       附加'/'，'='，'*'，'@'，'|'或按ls -F的'>'。
--inodes 打印每个文件的索引节点号。
--device 打印每个文件所属的设备ID号。

------- 排序选项 -------
-v          按版本字母顺序对文件进行排序。
-t          按上次修改时间对文件排序。
-c          按上次状态更改时间对文件排序。
-U          不排序文件。
-r          颠倒排序顺序。
--dirsfirst 在文件之前列出目录（-U禁用）。
--sort X    选择排序：名称，版本，大小，mtime，ctime。

------- 图形选项 -------
-i    不打印缩进线。
-A    打印ANSI线图形缩进线。
-S    使用CP437（控制台）图形缩进线打印。
-n    始终关闭着色（-C替代）。
-C    始终打开着色。

------- XML / HTML / JSON选项 -------
-X        打印树的XML表示形式。
-J        打印树的JSON表示形式。
-H        baseHREF打印出以baseHREF作为顶层目录的HTML格式。
-T        字符串用字符串替换默认的HTML标题和H1标头。
--nolinks 关闭HTML输出中的超链接。

------- 输入选项 -------
--fromfile 从文件中读取路径（。= stdin）

------- 其他选项 -------
--version 打印版本并退出。
--help    打印用法和此帮助消息并退出。

-选项处理终止符。
```

## 展示效果

```shell
➜  app tree   
.
├── Console
│  └── Kernel.php
├── Exceptions
│  └── Handler.php
├── Http
│  ├── Controllers
│  │  ├── Auth
│  │  │  ├── ForgotPasswordController.php
│  │  │  ├── LoginController.php
│  │  │  ├── RegisterController.php
│  │  │  ├── ResetPasswordController.php
│  │  │  └── VerificationController.php
│  │  ├── Controller.php
│  │  └── IndexController.php
│  ├── Kernel.php
│  └── Middleware
│      ├── Authenticate.php
│      ├── CheckForMaintenanceMode.php
│      ├── EncryptCookies.php
│      ├── RedirectIfAuthenticated.php
│      ├── TrimStrings.php
│      ├── TrustProxies.php
│      └── VerifyCsrfToken.php
├── Providers
│  ├── AppServiceProvider.php
│  ├── AuthServiceProvider.php
│  ├── BroadcastServiceProvider.php
│  ├── EventServiceProvider.php
│  └── RouteServiceProvider.php
└── User.php

7 directories, 23 files
```