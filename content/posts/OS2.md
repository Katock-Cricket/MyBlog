---
title: "OS笔记2：系统引导"
date: 2022-05-15T22:58:33+08:00
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

# 二、系统引导

### MIPS启动流程

两个阶段：Stage1（汇编代码），Stage2（C代码）

四个虚拟空间：kseg0（最高位置0得物理地址，cache存取），kseg1（高三位置0，不用cache，重启时可正常工作），kseg2（内核态，MMU转换），kuseg（用户态，MMU转换）

#### Stage1

![1.png](1.png)

#### Stage2

![2.png](2.png)

### MIPS下Linux启动流程

1. Bootloader将 Linux 内核映像拷贝到RAM
2. 从head.s文件的kernel_entry()开始，初始化内核堆栈
3. ……

### ELF文件

![3.png](3.png)

## 测试题

Bootloader的实现严重依赖于具体硬件，即使是相同的CPU，它的外设(比如Flash)也可能不同，所以不可能有一个Bootloader支持所有的CPU、所有的开发板。但是bootloader是可以支持不同CPU架构和不同操作系统，只不过常常不能直接拿来用，需要做一些移植的工作。



MIPS平台外设IO空间通常映射到kseg1段，映射到0xA0000000-0xBFFFFFFF的不通过cache的空间中。



cc1是预处理器和编译器，as是汇编器，collect2是链接器，他们都是gcc所包含的工具。



C语言要支持不定个数的参数，其压栈顺序就必然是从右至左。



C程序真正的入口点是_start，它首先做一些初始化工作（启动例程， Startup Routine），然后调用C代码中提供的main函数。



Linux中sys_execve()只是函数do_execve()的一个界面，实际的处理加载可执行文件动作在do_execve()中完成。
