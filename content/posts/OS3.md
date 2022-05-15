---
title: "OS笔记3：内存管理"
date: 2022-05-15T22:58:37+08:00
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

# 三、存储管理

### 分区存储

#### 固定分区

当系统初始化时，把存储空间划分成若干个任意大小的区域；然后，把这些区域分配给每个用户作业

- 优点 ：易于实现，开销小。
- 缺点：内碎片造成浪费，分区总数固定，限制了并发执行的程序数目。
- 分配方式：单一队列与多队列

#### 可变式分区

分区的边界可以移动，即分区的大小可变。没有内碎片，但是有外碎片。

碎片：内存中无法被利用的存储空间。

外碎片：分区与分区之间的碎片，可以用紧凑技术消除外碎片。

- 空闲空间管理：位图和空闲链表
- 位图表示法：给每个分配单元赋予一个字位，字位取值为0表示单元闲置，取值为1则表示已被占用。
- 链表表示法：由“标志位(空闲或程序)+起始地址+空间长度+链表指针”构成。

![1.png](1.png)

### 分配算法

**首次适应算法**：从这个空白区域链的始端开始查找，选择第一个足以满足请求的空白块。

低地址部分留下了很多很小空闲空间，增加了查找开销。



**下次适应算法**：每次为存储请求查找合适的分区时，总是从上次分配的下一块开始，只要找到一个足够大的空白区，就将它划分后分配出去。

使小空闲分区均匀分布，但会导致缺乏大空闲分区。



**最佳适应算法**：总是寻找其大小最接近于作业所要求的存储区域(将空闲分区按容量递增排列)。

留下许多小分区（碎片）。



**最坏适应算法**：总是寻找最大的空白区(将空闲分区按容量递减排列)。

当大作业来时，可能没有那么大的空间了。



### 页式存储

小页面：

1. 减少页内碎片和总的内存碎片，有利于提高内存利用率

2. 每个进程页面数增多，使页表长度增加，占用内存较大

大页面：

1. 每个进程页面数减少，页表长度减少，占用内存较小
2. 增加页内碎片增大，不利于提高内存利用率

反置页表：

依据内存中的物理页面号来组织，表项为进程pid+逻辑页号p。如果检索到与之匹配的表项，则表项的序号 i 便是该页的物理块号，将该块号与页内地址一起构成物理地址。反置页表的大小只与物理内存的大小相关，与逻辑空间大小和进程数无关。



### 段式存储

每个进程一张段表，每个段一张页表。段表含页表始址和页表长度。页表含物理页号。

![2.png](2.png)



### 缺页异常处理流程

1. 陷入内核态，保存寄存器信息保存现场
2. 找到发生缺页的虚拟页面
3. 检查虚拟地址的有效性和保护位
4. 找一个空闲的页框(物理内存中的页面)，如果没有的话使用页面置换算法换出一个页框
5. 如果换出去的页框是dirty的，需要写回磁盘
6. 准备好页框后将所需页面从磁盘调入
7. 向操作系统发出中断更新页表
8. 恢复现场继续执行



### 页面置换策略

Belady现象：分配的页面数增多但缺页率反而提高的异常现象

**OPT**：最优置换，无法被实现，但可以用于衡量其他页面置换算法的效果。

**FIFO**：先入先出，当必需置换掉某页时，选择最旧的页换出。

**Second Chance** ：每个页面都有一个标志位。用于标识此数据放入缓存队列后是否被再次访问过。删的时候如果没被访问过直接删，如果访问过的话清除标志然后跳过删下一个，这个下次再删。FIFO的改进版。

**clock**：环形队列，没被访问过直接替换，被访问过的话清除标志，指向下一个。Second Chance改进版。

![3.png](3.png)

**LRU**：每访问到一个已有的页，把它移到队尾，置换时选择队首页。

**AGING**：老化算法，每个页面一个移位寄存器，每隔一段时间右移一位，访问一次就在最高位写1，置换时选数值最小的。



### 页目录自映射

自映射：页表中有一项是页目录，页目录中有一项指向页目录物理地址

对于一个4G的存储空间，有1024个4M大小的页，其中页表4M；页表有1024个4K大小的表项，其中页目录4K。给定页表基地址PTbase（4M对齐即低22位都是0）

1. 页目录基地址：PDbase = PTbase | （PTbase >> 10）

解析：PTbase相对于0，有 PTbase/4M 即 PTbase >> 22 个页，设为`xt`页

​			PDbase相对于PTbase，有（PDbase - PTbase）/4K 即（PDbase - PTbase）<< 12 个页表项，设为`xd`			个页表项

​			因为`xt` = `xd` ，这些页和页表项一一对应。

​			所以联立得PDbase = PTbase + （PTbase >> 10）

2. 自映射目录项：PDEself-mapping = PTbase | （PTbase >> 10）| （PTbase >> 20）

解析：PTbase相对于0，有 PTbase/4M 即 PTbase >> 22 个页，设为`xt`页

​			PDbase相对于PTbase，有（PDbase - PTbase）/4K 即（PDbase - PTbase）<< 12 个页表项，设为`xd`			个页表项

​			PDEself相对于PDbase，有（PDEself - PDbase）/4 即（PDEself - PDbase）<< 2 个页目录项，设为			`xe`个页目录项

​			因为`xt` = `xd` =`xe`，这些页、页表项和页目录项一一对应；

​			所以联立消PDbase得 PDEself-mapping = PTbase + （PTbase >> 10）+ （PTbase >> 20）



### ELF文件

![4.png](4.png)

.bss						此节存放用于程序内存映象的未初始化数据。此节类型是SHT_NOBITS,因此不占文件空间。

.data和.datal		此节存放用于程序内存映象的初始化数据。

.text						此节存放正文，也称程序的执行指令。
