---
title: "【NCRE四级网络工程师】操作系统多选题"
date: 2019-02-26T16:13:53+08:00
keywords: "NCRE"
comments: true
tags: ["NCRE"]
categories: ["debuginn"]
image: "https://static.debuginn.com/202303261146594.jpg"
---

**保存在进程控制块中的是**

- 进程标识符
- 进程当前状态
- 代码段指针

> PCB的内容可以分为调度信息和现场信息两大部分。调度信息供进程调度时使用。调度信息供进程调度时使用，描述了进程当前所处的状态，包括进程名、进程号、存储信息、优先级、当前状态、资源清单、家族关系、消息队列指针、当前打开文件等。

**下列关于地址映射的叙述中，正确的是：**

- 地址映射过程通常是有硬件完成的； 
- 地址映射是将虚拟地址转换为物理地址； 
- 页表项的一些内容是由硬件决定的； 
- 根据页表项的有效位确定所需访问的页面时都已经在内存。

**下列对于块表的叙述中，正确的是：**

- 块表的另一个名称是TLB 
- 当切换进程时，要刷新快表； 
- 快表存放在高速缓存中； 
- 对块表的查找是按内容并行进行的。

**下列各项中，那些事文件控制块中必须保存的信息？**

- 文件名 
- 文件大小 
- 文件创建时间 
- 磁盘块起始地址

> 文件控制块FCB包括：文件名、用户名、文件号、文件地址、文件长度、文件类型、文件属性、共享技术、文件的建立日期、保存期限、最后修改日期、最后访问日期、口令、文件文件物理结构等等。

**设计文件系统时应尽量减少访问磁盘的次数，以提高文件系统的性能。下列各项措施中，哪些可以提高文件系统的性能？**

- 块高速缓存 
- 磁盘驱动调度 
- 目录项分解法

**设备与CPU之间的数据传送和控制方式有多种，他们是：**

- 程序直接控制方式
- 中断控制方式
- DMA方式
- 通道控制方式

**当前测到系统发生死锁之后，解除死锁的方法是？**

- 剥夺某些进程所占有的资源； 
- 撤销某些进程 
- 从新启动系统

**测试与设置指令（Test&Set）是解决互斥访问临界区的硬件方法。下列关于该指令功能的叙述中，哪些是正确的**

- A) 测试W的值，若W=1，则返回重新测试 
- B) 测试W的值，若W=0，置位W=1，进入临界区 
- C) 退出临界区时，复位W=0

> **TS指令实现互斥的算法**是：测试锁变量的值，如为1，则重复执行本命令，不断重复测试变量的值；如为0，则立即将锁变量测值置为1，进入临界区；测试并设置指令是一条完整的指令，而在一条指令的执行中间是不会被中断的，保证了锁的测试和关闭的连续性；退出临界区时，将锁变量测试值设为0。

**下列关于虚拟存储器的叙述中，哪些是正确的？**

- 在虚拟存储系统中，进程的部分程序装入后便可运行 
- 虚拟存储技术允许用户使用比物理内存更大的存储空间 
- 实现虚存必须有硬件支持

> 段页式存储管理为用户提供了一个二维地址空间，满足程序和信息的逻辑分段的要求。其基本思想是用页式方法来分配和管理内存空间，即把内存划分为若干大小相等的页面。内存是以页为基本单位分配给每个用户程序的，逻辑上相邻的页面在物理内存中不一定相邻。内存空间最小的单位是页而不是段。页式存储管理的特征是等分内存，有效的克服了碎片，提高了存储器的利用率。

**下列文件的物理结构中，哪些结构适合文件的随机存取**

- 连续结构
- 索引结构
- 多级索引结构

**在程序控制I/O方式中，若输出设备向处理机返回“准备就绪”信号，则表示（）。**

- 输出缓冲区已空
- 可以向输出缓冲区写数据

**在设备分配中，预防死锁的策略包括（）。**

- A) 建立SPOOLing系统 
- B) 一次分配所有资源 
- C) 有序分配资源 
- D) 剥夺其他进程的资源

在设计系统时确定资源分配算法，限制进程对资源的申请，从而保证不发生死锁。具体的做法是破坏产生死锁的四个必要条件之一：

①破坏“互斥条件”：可以通过采用假脱机（SPOOLing）技术，允许若干个进程同时输出；

②破坏“不可剥夺”条件：如果资源没有被等待进程占有，那么该进程必须等待，在其等待过程中，其资源也有可能被剥夺；

③破坏“请求和保持”条件：可以采用静态分配资源策略，将满足进程条件的资源一次性分配给进程，也可以采用动态资源分配，即需要资源时才提出申请，系统在进行分配；

④破坏“循环等待”条件：进程申请资源时，必须严格按照资源编号的顺序进行，否则系统不予分配。

**下列关于进程的叙述中，哪些是正确的（ BC ）。**

- A) 一个进程的状态变化必定会引起另一个进程的状态变化
- B) 信号量的初值一定大于等于零
- C) 进程是资源分配的基本单位，线程是处理机调度的基本单位
- D) 进程被挂起后，它的状态一定为阻塞态 
- E) 操作系统中引入P、V操作主要是为了解决死锁问题

**在下列存储管理方案中，能支持多道程序设计的是（ ）。**

- A) 可变分区存储管理 
- B) 页式存储管理 
- C) 单一分区存储管理 
- D) 固定分区存储管理 
- E) 段页式存储管理

> 单一分区存储器管理，只充许一道程序独占内存空间，因此不能支持多道程序设计技术。

**在计算机系统中，形成死锁的必要条件是（ ABCD ）。**

- A) 资源互斥使用 
- B) 部分分配资源 
- C) 已分配资源不可剥夺 
- D) 资源申请形成环路 
- E) 系统资源不足

**当前Android操作系统应用广泛，它具有下列哪些特性（ BC ）。**

- A) 批处理
- B) 移动应用
- C) 支持网络
- D) 分布式 
- E) 兼容性

**下列关于进程控制块的叙述中，哪些是正确的（ ABC  ）。**

- A) 进程控制块的英文缩写是PCB
- B) 每个进程都拥有自己的进程控制块
- C) 进程控制块必须常驻内存 
- D) 进程控制块必须指明其兄弟进程的进程号 
- E) 进程创建完毕后，系统将其进程控制块插入等待队列

**下列关于信号量使用的叙述中，哪些是正确的（ ABD ）。**

- A) 信号量初始化后，只能实施P、V原语操作 
- B) 在互斥信号量与同步信号量都使用的进程中，应先执行同步信号量的P操作 
- C) 在互斥信号量与同步信号量都使用的进程中，应先执行同步信号量的V操作 
- D) 信号量的初值不能小于0 
- E) 互斥信号量的变化范围只能是正整数

**下列页面置换算法中，哪些算法需要用到访问位（引用位）（ CDE ）。**

- A) 先进先出算法FIFO 
- B) 最佳置换算法OPT 
- C) 最近最久未使用算法LRU 
- D) 时钟算法CLOCK 
- E) 最近未使用算法NRU

> 从简单页式存储管理方案发展到虚拟页式存储管理方案，页表项中通常需要增加的信息有：有效位，修改位，访问位。

**SPOOLing系统的主要组成部分是（ ABC ）。**

- A) 输入井和输出井 
- B) 输入缓冲区和输出缓冲区 
- C) 输入进程和输出进程 
- D) 输入控制器和输出控制器 
- E) 输入分配器和互斥分配器

**下列关于死锁的叙述中，哪些是正确的（ ABC ）。**

- A) 死锁产生的原因是进程推进顺序不当 
- B) 环路是死锁产生的必要条件 
- C) 采用银行家算法能有效地实现死锁避免 
- D) 当系统中只有一个进程时也可能会产生死锁 
- E) 系统出现死锁是因为进程调度不当

**进程（线程）调度的主要功能有（ ABCD ）。**

- A) 根据一定的调度算法选择被调度的进程（线程） 
- B) 将CPU分配给选中的进程（线程） 
- C) 将换下CPU的进程（线程）的现场信息保存到进程控制块中 
- D) 将选中的进程（线程）的现场信息送入到相应寄存器中 
- E) 将阻塞的进程（线程）唤醒并置为就绪状态

**下列哪一种存储管理方案以一个进程为单位分配一组连续的内存单元（ AB ）。**

- A) 固定分区 
- B) 可变分区 
- C) 页式 
- D) 段式 
- E) 段页式

**在虚拟页式存储方案中，当判断一个页面是否已调入内存时需要用到页表表项的哪些位（ AB ）。**

- A) 驻留位 
- B) 中断位 
- C) 修改位 
- D) 访问位 
- E) 保护位

**下列哪些文件是按照文件的组织形式划分的文件类型（ BDE ）。**

- A) 系统文件 
- B) 普通文件 
- C) 临时文件 
- D) 目录文件 
- E) 特殊文件