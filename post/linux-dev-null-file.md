---
title: "Linux >/dev/null 2>&1 命令使用说明"
date: 2020-01-13T19:34:52+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "linux,shell"
comments: true
weight: 0
tags: ["linux","shell"]
categories: ["OS"]
image: "https://webp.debuginn.com/202303161935472.jpg"
---

近期在开发项目中遇到了PHP使用`shell_exec`执行 Shell 命令的问题，具体说是 Shell 使用 FFmpeg 软件进行录制直播流，但是 PHP 等待命令执行时间是有限的，并且会出现等待时间过长导致该执行接口出现未响应问题，寻找了网上方法，发现了一种比较好的方式，就是将要执行的Shell语句后面加上`>/dev/null 2>&1`这段特殊的命令，简单来说就是将执行的 Shell 操作放到后台进行运行，将快捷反应执行的操作，解决执行 Shell 语句等待问题，接下来就是对这段命令的解析：

**命令的结果可以通过%>的形式来定义输出。**

- `/dev/null` 代表空设备文件
- `>` 代表重定向到哪里，例如：`echo "123" > /home/123.txt`； 
- `1` 表示 stdout 标准输出，系统默认值是 1，所以`>/dev/null`等同于`1>/dev/null`； 
- `2` 表示 stderr 标准错误； 
- `&` 表示等同于的意思，`2>&1`，表示 2 的输出重定向等同于 1。

**针对下面数字的代表含义的解释：**

- 0:表示键盘输入(stdin)
- 1:表示标准输出(stdout),系统默认是1 
- 2:表示错误输出(stderr)

**`> /dev/null 2>&1` 语句含义：**

`> /dev/null` ： 首先表示标准输出重定向到空设备文件，也就是不输出任何信息到终端，说白了就是不显示任何信息。
`2>&1` ：接着，标准错误输出重定向（等同于）标准输出，因为之前标准输出已经重定向到了空设备文件，所以标准错误输出也重定向到空设备文件。