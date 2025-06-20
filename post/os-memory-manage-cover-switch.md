---
title: "操作系统 内存管理 覆盖与交换技术"
date: 2017-12-27T19:38:26+08:00
keywords: "os,introduction"
comments: true
tags: ["os", "system"]
categories: ["OS"]
image: "https://static.debuginn.com/202302221853276.jpg"
---

## 覆盖技术

覆盖技术是指一个程序的若干程序段和几个程序的某些部分共享一个存储空间。覆盖技术的实现是把程序分为若干个功能上相对独立的程序，按照其自身的逻辑结构使那些不会同时执行的程序段共享同一块内存区域。未执行的程序段先保存在磁盘上，当有关程序段的前一部分执行结束后，把后续程序段调入内存，覆盖前面的程序段。

覆盖技术是用户程序自己附加的控制。要把一个程序划分成不同的程序段，并规定好他们的执行和覆盖顺序。操作系统则根据程序员提供的覆盖结构，完成程序段之间的覆盖。

该程序正文段所需要的内存空间是A（20KB）+B（50KB）+F（30KB）+C（30KB）+D（20KB）+E（40KB）=190KB，但是在采用了覆盖技术后只需要A（20KB）+B（50KB）+E（40KB）=110KB占用空间。

![覆盖技术](https://static.debuginn.com/202304131939488.png)

覆盖技术主要用于系统程序的内存管理上，MS-DOS系统分为两个部分。

- 操作系统中经常要用到的基本部分，它们常驻在内存且占用固定区域。 
- 不太经常使用的部分，它们存放在磁盘上，当调用它们时才被调入内存覆盖区。

## 交换技术

交换技术：在分时系统中，用户的进程比内存能容纳的数量要多，这就需要在磁盘上保存那些内存放不下的进程。在需要运行这些进程时，再将它们装入内存。

进程从内存移到磁盘并再移动回内存称为交换。交换技术是进程在内存与外存之间的动态调度，是由操作系统控制的。

后备存储区（又称盘交换区）。

目的：尽可能达到”足够快的交换进程，以使当CPU调度程序想重新调度CPU时，总有进程在内存中处于就绪（准备执行）状态“的理想状态，从而提高内存利用率。

**交换技术的原理：**

（1）换出进程的选择：系统需要将内存中的进程换出时，应该选择那个进程？

根据时间片轮转法或基于优先数的调度算法来选择要换出的进程。

（2）交换时间的确定

在内存空间不够或有不够的危险时，还出内存中的部分进程到外存，以释放所需要的内存。

（3）交换空间的分配

在一些系统中，当进程在内存中时，不再外塔分配磁盘空间。当它被换出时，必须为它分配磁盘交换空间。

在另一些系统中，进程一但创建，就分配给它磁盘上的交换空间。无论何时程序被换出，他都被换到已经为它分配的空间，而不是每次换到不同的空间。

（4）换入进程换回内存时位置的确定

绝对地址：在原来的位置上；

相对地址：可再进行地址重定位。

交换技术的缺点：

由于交换时需要花费大量的CPU时间，这将影响对用户的响应时间，因此，减少交换的信息量是交换技术的关键问题。

合理的做法：

在外存中保留每个程序的交换副本，换出时仅将执行时修改过的部分复制到外存。

**覆盖技术和交换技术的发展导致了虚拟存储技术的出现。**