---
title: "优雅的使用 Brew 切换 Go 版本"
date: 2020-11-01T20:15:32+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "go,brew"
comments: true
tags: ["go","brew"]
categories: ["golang"]
image: "https://static.debuginn.com/202302282016622.jpg"
---

Brew 是 Mac 上包管理工具，和 Linux 上的 apt 、yum、rpm 一样，可以提供非图形化软件的安装，昨天在打造宇宙最强 IDE 的时候，使用brew工具更新了一下软件包，是我的 Go 版本升级到了最新版本，同时之前配置的多版本 Go 抹掉了，现在写一下记录，你如果需要的话可以使用一下。

之前写过一个使用 GVM 版本管理工具的文章，这个是第三方工具管理的，都比较好用，你可以根据自己的需求安装。

## 方案一 brew switch

### 1 brew install

```sybase
brew install go
```

### 2 brew switch

```sybase
~ brew info go
go: stable 1.15.3 (bottled), HEAD
```

使用 `brew info go` 命令你可以看到当前目前的 go 可以切换的版本，接下来就安装多个版本并且切换到对应的版本。

```sybase
// 安装指定 go 版本
brew install go@<version>
// forexample
brew install go@1.12.17
```

安装好了 之后使用 `brew info go` 查看是否可以切换了。

```sybase
brew switch go 1.12.17
```

单纯的使用上面的命令你会发现，go 不能使用了，并且会出现下面的提示：

```sybase
~ brew switch go 1.12.17
Cleaning /usr/local/Cellar/go/1.12.17
Cleaning /usr/local/Cellar/go/1.15.3
0 links created for /usr/local/Cellar/go/1.12.17
```

创建了零个连接，就代表着没有成功的将 go 版本指向你所需要的版本下，问题是什么呢？现将 go 版本切回 go 1.15.3，你会发现可以切换并正常使用：

```sybase
~ brew switch go 1.15.3
Cleaning /usr/local/Cellar/go/1.12.17
Cleaning /usr/local/Cellar/go/1.15.3
3 links created for /usr/local/Cellar/go/1.15.3

~ go version
go version go1.15.3 darwin/amd64
```

定位这个原因你需要看看为什么没有未给 go 1.12.17 版本创建软连接，首先要找一下 go 默认安装的位置，使用 `go env` 查看安装目录：

```sybase
/usr/local/Cellar/go/
```

_使用 brew 工具在 MacOS Catalina 系统安装的位置。_

进入到目录之后在 go 目录下只有刚才默认安装的 1.15.3 版本，并没有自己安装的版本，退出父级目录看到了下载的 go@1.12.17 版本，由于软连接连接的是上方的路径，需要将这个目录移动至 go 目录下：

```sybase
// 打开默认目录
cd /usr/local/Cellar/go/
// 退出目录
cd ..
// 移动目录至 go 目录下
mv go@1.12.17 go/
// 重要！！！ 重命名文件夹
mv go@1.12.17 1.12.17
```

接下来使用切换命令 `brew switch go <version>` 就可以切换环境了。

## 方案二 brew link

使用 Homebrew 3.2.9 验证。

1、安装新的版本：

```sybase
brew install go@1.16 // 安装 go 1.16 版本
```

2、移除原有的 go 版本软链

```sybase
brew unlink go
```

3、指定新的版本软链

```sybase
brew link go@1.16
```