---
title: "OS笔记4：进程与并发程序设计"
date: 2022-05-15T22:58:42+08:00
author: "Me"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "Desc Text."
canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
editPost:
    URL: "https://github.com/Katock-Cricket/MyBlog/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

# 四、进程与并发程序设计

### 并发与并行

并发：两个活动在同一时间各自处在起点和终点之间的某一处，**不一定并行**（不一定都在处理机上）

并行：两个程序在同一时间度量下同时运行在不同的处理机上

### 进程与程序

进程：数据+程序+PCB（进程管理块）

通过多次执行，一个程序对应多个进程；通过调用，一个进程包括多个程序

### 进程原语

- 指令序列执行是连续的，不可分割
- 是操作系统核心组成部分
- 必须在管态（内核态）下执行，且常驻内存

### 进程三状态

就绪、执行、等待

![1.png](1.png)



### 进程与线程

资源拥有者称为进程，可执行单元称为线程。将资源与计算分离，提高并发效率；

线程作用：减小进程切换的开销；提高进程内的并发程度；共享资源

- 一个进程可以拥有多个线程，而一个线程同时只能被一个进程所拥有
- 进程是资源分配的基本单位，线程是处理机调度的基本单位，**所有的线程共享其所属进程的所有资源与代码**
- **线程共享进程的数据**的同时，**线程有自己私有的的堆栈**



### 进程通信

**临界资源**：一次仅允许一个进程访问的资源

**临界区**：每个进程中访问临界资源的那段代码

**同步**：直接制约，逻辑上的依赖

**互斥**：间接制约，资源的等待

**同步互斥原则**：

1. 空闲让进：临界资源处于空闲状态，允许进程进入临界区
2. 忙则等待：临界区有正在执行的进程，所有其他进程则不可以进入临界区
3. 有限等待：对要求访问临界区的进程，应在保证在有限时间内进入自己的临界区，避免死等
4. 让权等待：当进程（长时间）不能进入自己的临界区时，应立即释放处理机，尽量避免忙等

**信号量**：PV操作

**管程**：分散的临界区集中起来，为每个可共享资源设计一个专门机构来统一管理各进程对该资源的访问，管程是一种语言概念，由编译器负责实现互斥，任一时刻，管程中只能有一个活跃进程。



### 调度算法

1. 短作业优先
2. 先来先服务
3. 最短剩余时间SRTF
4. 最高相应比优先HRRF
5. 时间片轮转算法
6. 优先级算法



### 死锁

死锁、活锁、饥饿

死锁发生的**必要条件**：互斥、请求和占有、不可剥夺、环路等待

安全序列：序列中每个进程需要的附加资源 <= 当前可用资源 + 它前面所有进程占有的资源

银行家算法：先看request（当前进程请求资源量）是否小于need（当前进程可能需要的最大资源量-当前占有量），小于的话再看request是否小于available（当前还有多少可用），如果都行的话假设分配一下用安全性算法检测，若安全则分配否则不分配

安全性算法：找need<work的进程，有的话，这个进程对应的finish数组置true，work+=allocate，初始时work=available，如果最后所有进程全为true则安全

RAG资源分配图算法……
