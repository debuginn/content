---
title: "使用 GVM 工具管理 Go 版本"
date: 2020-07-12T16:32:00+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "go,gvm"
comments: true
weight: 0
tags: ["go","gvm"]
categories: ["golang"]
image: "https://static.debuginn.com/202303011938210.jpg"
---

在 Go 项目开发中，团队要保持开发版本一致，怎么能够快速的安装及部署并且切换 Go 环境，在这里推荐一款工具 GVM （ Go Version Manager ），它可以便捷切换与自定义 Go Path 、Go Root 等参数，是一款实打实的多版本安装及管理利器。

GVM，类似于ruby 中的 [RVM](https://rvm.io/)，java 中的 [jenv](https://github.com/linux-china/jenv)（国产），可用于方便管理 Go 的版本，它有如下几个主要特性：

管理 Go 的多个版本，包括安装、卸载和指定使用 Go 的某个版本；

- 查看官方所有可用的 Go 版本，同时可以查看本地已安装和默认使用的 Go 版本； 
- 管理多个 GOPATH，并可编辑 Go 的环境变量； 
- 可将当前目录关联到 GOPATH； 
- 可以查看 GOROOT 下的文件差异。

## 安装 Installing

```sybase
bash < <(curl -s -S -L https://raw.githubusercontent.com/moovweb/gvm/master/binscripts/gvm-installer)
```
或者，如果您使用的是 zsh，只需使用 zsh 更改 bash。

##  使用 GVM

使用 gvm 可以查看支持的操作：

```shell
➜  ~ gvm     
Usage: gvm [command]

Description:
  GVM is the Go Version Manager

Commands:
  version    - print the gvm version number
  get        - gets the latest code (for debugging)
  use        - select a go version to use (--default to set permanently)
  diff       - view changes to Go root
  help       - display this usage text
  implode    - completely remove gvm
  install    - install go versions
  uninstall  - uninstall go versions
  cross      - install go cross compilers
  linkthis   - link this directory into GOPATH
  list       - list installed go versions
  listall    - list available versions
  alias      - manage go version aliases
  pkgset     - manage go packages sets
  pkgenv     - edit the environment for a package set
```

## 安装 Go 版本

例如安装 go1.13 版本：

```shell
gvm install go1.13
```

## 查看 Go 版本

```shell
➜  ~ gvm list          

gvm gos (installed)

   go1.12
=> system
```

## 切换 Go 版本

```shell
gvm use go1.**
```

## 管理 Gopath 环境

GVM 提供了一个比较简单的工具 gvm pkgset 可以创建使用 GOPATH 环境：

```shell
➜  ~ gvm pkgset
= gvm pkgset

* http://github.com/moovweb/gvm
== DESCRIPTION:
GVM pkgset is used to manage various Go packages
== Usage
  gvm pkgset Command

== Command
  create     - create a new package set
  delete     - delete a package set
  use        - select where gb and goinstall target and link
  empty      - remove all code and compiled binaries from package set
  list       - list installed go packages
```

## 卸载 Uninstall

卸载某个安装好的 Go 版本：

```shell
gvm uninstall go1.13
```

## 开源代码

GVM 是一款使用 Shell 脚本实现的便捷工具，作为开源项目，推荐大家给一个 Star 支持。

[https://github.com/moovweb/gvm](https://github.com/moovweb/gvm)