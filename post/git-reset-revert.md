---
title: "Git 命令 reset 和 revert 的区别"
date: 2021-09-21T17:33:00+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "git,github,reset,revert"
comments: true
weight: 0
tags: ["git","github","reset","revert"]
categories: ["git"]
image: "https://static.debuginn.com/202302262207101.jpg"
---

## 前言

在团队开发中，使用 Git 作为版本开发工具，可以便捷地协同多人管理并行开发，但是由于自己或者其他人代码提交污染了远程分支，就需要对远程代码进行恢复操作，Git 提供了 reset 和 revert 两种命令来进行恢复操作，这两种操作效果是截然不同的，不太清楚这个原理的同学需要了解一下，以免在实际的开发过程中翻车，导致线上远程仓库不可逆转的操作。

首先从英文释义来讲，reset 是重置的意思，revert 是恢复、还原的意思，作为 Coder ，第一感觉 reset 的效果比 revert 更猛一些，实际情况也的确如此，让我们一起探讨一下吧。

![git](https://static.debuginn.com/202302262208546.png)

## 背景

Git 的每一次提交都是一次 commit，上图可以看到在时间线上有三次提交，此时 HEAD 指向 main 分支，main 分支又指向最新的 Commit3。

- HEAD 是指向当前分支的最新提交的指针，可以在任意分支进行切换；
- main （master）分支，是一个 git 代码仓库的主分支也是默认分支；
- commit 每一次提交代码都会产生一个 commit id 来标识工作区的变更与改动。

## 实践出真理

为了直接明白的了解其原理，我这里在 github 上创建一个空白的仓库，按照上图创建三次提交：

```shell
commit b0ef8f9125226af8f06ff1aba7c1f1fc83adea9b (HEAD -> master, origin/master)
Author: debuginn <debuginn@icloud.com>
Date:   Tue Sep 21 16:36:39 2021 +0800

    feat add 3.go

commit 338bf3e30983d34074f37a18b3ff80ea9bca75f0
Author: debuginn <debuginn@icloud.com>
Date:   Tue Sep 21 16:36:09 2021 +0800

    feat add 2.go

commit 6b166ed34962da08d944e2b1d3f36d9015dd8f35
Author: debuginn <debuginn@icloud.com>
Date:   Tue Sep 21 16:35:16 2021 +0800

    feat add 1.go
```

### Git Reset

`git reset` 的作用是将 HEAD 指向指定的版本上去：

![git reset](https://static.debuginn.com/202302262210014.png)

**1 使用 git log 查看提交记录：**

```shell
commit b0ef8f9125226af8f06ff1aba7c1f1fc83adea9b (HEAD -> master, origin/master)
Author: debuginn <debuginn@icloud.com>
Date:   Tue Sep 21 16:36:39 2021 +0800

    feat add 3.go

commit 338bf3e30983d34074f37a18b3ff80ea9bca75f0
Author: debuginn <debuginn@icloud.com>
Date:   Tue Sep 21 16:36:09 2021 +0800

    feat add 2.go

commit 6b166ed34962da08d944e2b1d3f36d9015dd8f35
Author: debuginn <debuginn@icloud.com>
Date:   Tue Sep 21 16:35:16 2021 +0800

    feat add 1.go
```

这里可以看到我们提交了三次记录，我们现在想恢复到第一次 commit 提交的时候。

**2 使用 git reset --hard 命令操作：**

```shell
➜  demo git:(master) git reset --hard 6b166ed34962da08d944e2b1d3f36d9015dd8f35
HEAD 现在位于 6b166ed feat add 1.go
```

再次查看 git log :

```shell
commit 6b166ed34962da08d944e2b1d3f36d9015dd8f35 (HEAD -> master)
Author: debuginn <debuginn@icloud.com>
Date:   Tue Sep 21 16:35:16 2021 +0800

    feat add 1.go
```

此时我们可以看到已经恢复到了第一次提交代码的时候，目前我们是使用 `git reset --hard` 的方式，其实这里存在着三种方式，TODO 下一篇 git 操作讲一下。

这时候我们只是讲本地的 HEAD 指向了 main 分支的 commit 1，但是远程并没有变更，此时需要强行推一下就可以了。

**3 使用git push -f 强行推送到远程：**

```shell
➜  demo git:(master) git push -f
总共 0（差异 0），复用 0（差异 0），包复用 0
To github.com:debuginn/demo.git
 + b98f95e...6b166ed master -> master (forced update)
```

![github](https://static.debuginn.com/202302262212430.png)

此时我们可以看到远程也没有了我们之前提交的三次记录而是只有第一次的提交记录。

在团队合作的共同操作一个仓库的时候， git reset 命令一定要慎重使用，在使用的时候一定要再三确认其他同学的代码是否会被重置操作而导致代码丢失，导致一些提交记录的丢失，这些都是不可逆的，一定要慎重。

### Git revert

`git revert` 是用来重做某一个 commit 提交的内容，在我们原始的提交之中，我们会发现分支上面有创建了一个新的 commit 提交，而此时我们对于想重做的某个 commit 提交的内容都不存在了：

![git revert](https://static.debuginn.com/202302262214658.png)

**1 使用git log查看提交记录：**

```shell
Author: debuginn <debuginn@icloud.com>
Date:   Tue Sep 21 16:36:39 2021 +0800

    feat add 3.go
```

**2 使用git revert命令重做操作：**

```shell
➜  demo git:(master) git revert 338bf3e30983d34074f37a18b3ff80ea9bca75f0
删除 2.go
[master ef822b7] Revert "feat add 2.go"
 1 file changed, 9 deletions(-)
 delete mode 100644 2.go
```

再次查看 git log :

```shell
commit ef822b71c33a2dbbdaa350fddcfa14e8fc55e543 (HEAD -> master, origin/master)
Author: debuginn <debuginn@icloud.com>
Date:   Tue Sep 21 17:12:00 2021 +0800

    Revert "feat add 2.go"

    This reverts commit 338bf3e30983d34074f37a18b3ff80ea9bca75f0.

commit b0ef8f9125226af8f06ff1aba7c1f1fc83adea9b
Author: debuginn <debuginn@icloud.com>
Date:   Tue Sep 21 17:05:39 2021 +0800

    feat add 3.go
```

可以看到当前已经重做了一下 commit 2 的提交，已经讲 2.go 删除掉了。

![github](https://static.debuginn.com/202302262217507.png)

可以看到 github 上面有了四次提交记录。

## 总结

git reset和git revert都是属于重新恢复工作区以及远程提交的方式，但这两种操作有着截然不同的结果：

- git reset是将之前的提交记录全部抹去，将 HEAD 指向自己重置的提交记录，对应的提交记录都不复存在；
- git revert 操作是将选择的某一次提交记录 重做，若之后又有提交，提交记录还存在，只是将指定提交的代码给清除掉。

选择合适的方式回滚自己的代码在团队合作中很重要，但是要慎重操作，不要丢失代码哦。
