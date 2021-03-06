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

### 覆盖与交换

覆盖：程序员做的，将一个大作业分割成若干小部分，当前一个部分运行结束后，其占用的内存被后面的部分直接覆盖

交换：程序员不用做，解决运行多道程序所出现的内存不足问题，交换出暂时不需要的作业。

### 分区存储

#### 固定分区

当系统初始化时，把存储空间划分成若干个任意大小的区域；然后，把这些区域分配给每个用户作业

- 优点 ：易于实现，开销小。
- 缺点：内碎片造成浪费，分区总数固定，限制了并发执行的程序数目。
- 分配方式：单一队列与多队列

#### 可变式分区

分区的边界可以移动，即分区的大小可变。没有内碎片，但是有外碎片。

内碎片：分区中无法被利用的存储空间。

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

1. 陷入内核态，保存必要的信息（OS及用户进程状态相关的信息）。（现场保护）
2. 查找出发生缺页中断的虚拟页面（进程地址空间中的页面）。这个虚拟页面的信息通常会保存在一个硬件寄存器中，如果没有的话，操作系统必须检索程序计数器，取出这条指令，用软件分析该指令，通过分析找出发生页面中断的虚拟页面。（页面定位）
3. 检查虚拟地址的有效性及安全保护位。如果发生保护错误，则杀死该进程。（权限检查）
4. 查找一个空闲的页框（物理内存中的页面），如果没有空闲页框则需要通过页面置换算法找到一个需要换出
     的页框。（新页面调入（1））
5. 如果找的页框中的内容被修改了，则需要将修改的内容保存到磁盘上¹。（注：此时需要将页框置为忙状态，以防页框被其它进程抢占掉）（旧页面写回）
6. 页框“干净”后，操作系统将保持在磁盘上的页面内容复制到该页框中²。（新页面调入（2））
7. 步骤5，6会引起写磁盘调用，发生上下文切换（在等待磁盘写的过程中让其它进程运行）。
8. 当磁盘中的页面内容全部装入页框后，向CPU发送一个中断。操作系统更新内存中的页表项，将虚拟页面映射的页框号更新为写入的页框，并将页框标记为正常状态。（更新页表）
9. 恢复缺页中断发生前的状态，将程序指针重新指向引起缺页中断的指令。（恢复现场）
10. 程序重新执行引发缺页中断的指令，进行存储访问。（继续执行）



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

1. 页目录基地址：**PDbase = PTbase | （PTbase >> 10）**

解析：PTbase相对于0，有 PTbase/4M 即 PTbase >> 22 个页，设为`xt`页

​			PDbase相对于PTbase，有（PDbase - PTbase）/4K 即（PDbase - PTbase）<< 12 个页表项，设为`xd`			个页表项

​			因为`xt` = `xd` ，这些页和页表项一一对应。

​			所以联立得PDbase = PTbase + （PTbase >> 10）

2. 自映射目录项：**PDEself-mapping = PTbase | （PTbase >> 10）| （PTbase >> 20）**

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

### 伙伴系统

略

### 纯分页内存管理

基本分页管理，没有页面置换，一次性为程序分配所需物理内存。支持多级页表但不是动态装入。

### 反置页表

用物理页号找逻辑页号和进程号，有多少物理页框就有多少页表项；是为了减小页表占用空间。

### 抖动

驻留内存的进程数目增加，即进程并发程度的提高，处理器利用率先上升，然后下降。即将过多的CPU资源用来换页而不是进程。

原因：并发进程数量过多、物理内存不足、缺页率快速上升

---



### 测试题

##### 计算机从内存RAM向缓存Cache传输数据的单位

Cache line

##### 构成高速缓存Cache的基本存储器件是

SRAM

##### 采用固定分区进行内存分配，容易产生

内碎片

##### 某次内存分配后，剩余3块空闲空间，大小分别为1k、10k、100k。这时按顺序来了一批4k、6k、95k的作业内存需求，哪种算法能够满足尽量多的作业：

Best fit 正确

Worst fit 错误

##### 内存紧缩中用的重定位技术与程序链接过程中的重定位

前者是运行时的动态重定位，程序所占用的内存实际发生了变化（为了消除碎片高效利用内存），程序中的地址要随之发生变化，典型的应用是java虚拟机；

后者是编译链接过程中的重定位，主要原因是编译时程序地址在内存中的地址不确定，当多个程序编译链接后计算出程序地址的操作。

##### 页式内存管理缺点

内碎片、访问页表存在延迟、页表占用空间

##### 页面大小是8KB，虚拟地址0x13345所表示的页内偏移是

0x1345，8K=1<<13，取地址低13位

##### 页表项中记录有哪些信息

页框号、标志位

##### 当TLB未命中时，后续会执行哪些处理

MMU访问页表获得页框号，根据页表项更新TLB

##### 假设虚拟地址有64位，页面大小为4KB，一个页表项占8个字节，如果采用一级页表，页表需要占用多少内存

地址空间2^64^B，一页2^12^B，一共2^52^页，一个表项2^3^B，则共2^55^B

##### 关于多级页表

能够减少页表占用内存的大小、有效的页表项中都会存储页框号、使用二级页表的平均访存性能优于一级页表，但是平均访问时间不会减少

##### 在采用按需调页的系统中，影响内存读写平均延迟的因素有

外存读写速度、TLB命中率、进程切换开销、换页算法

##### 一个32位页式内存管理系统，页面大小是4KB，采用二级页表管理，页表被映射到起始地址0xC000_0000的4MB地址空间，如果需要将虚拟地址0x8001_0000映射到物理地址0x0000_0000上，则需要修改虚拟地址0x____________________上的页表项

每4KB一页，则0x8001_0000所在的页是0x8001_0000 / 4K = 0x80010，第0x80010个页。

每个页占用4B的页表项，则该页所在页表项在页表中的偏移是0x80010 * 4B = 0x200040，

页表起始地址为0xc000_0000，则该页表项的地址为 0xc000_0000 + 0x200040 = 0xc020_0040。



**一个32位的虚拟存储系统有两级页表，其逻辑地址中，第22到31位是第一级页表（页目录）的索引，第12位到21位是第二级页表的索引，页内偏移占第0到11位。每个页表（目录）项包含20位物理页框号和12位标志位，其中最后1位为页有效位。**

![](5.png)

![](6.png)



###### 1

32位地址，对应2^32^B即4GB存储空间；页偏移量12位，对应2^12^B即4KB的页大小

###### 2

1. 0x0，页目录号0，查表得0000号页目录项的内容是0x0，其中页面有效位为0，缺页
2. 0x00803004，转二进制0000 0000 1000 0000 0011 0000 0000 0100，得页目录号0000 0000 10等于2，查表得0002号页目录项的内容是0x5001，转二进制0101 0000 0000 0001取高20位得到物理页框号5，页是4K对齐所以物理地址是0x5000，根据虚拟地址的二级页表号00 0000 0011等于3，查表页表第三项内容0x2 0001（有效），得物理页框号0x20，同理得物理地址0x2 0000，根据虚拟地址得页内偏移量4，从0x9000开始往后数4个字即8个十六进制位得到0x0032 6001，大段读0，小段读1。
3. 0x0040_2001 = 0b0000_0000_0100_0000_0010_0000_0000_0001，得页目录号00_0000_0001等于1，查表得物理地址0x1000，由虚拟地址二级页表项为2，查表得页面物理地址0x5000，由虚拟地址偏移量为1，查表得0
4. 要访问0x326028，首先得虚拟地址的页内偏移0x028=0000 0010 1000，它的页框基地址为0x326000，查表得在0x2 0000的第0001处，所以虚拟地址的二级页表号为1=00 0000 0001，然后查页目录得第0003号是0x2 0001，得页目录号3=00 0000 0011，合起来得虚拟地址0000 0000 1100 0000 0001 0000 0010 1000=0x00c0 1028
