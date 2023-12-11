---
title: "VC和SA中文字幕修改教程"
date: 2023-12-11T18:32:22+08:00
draft: true
author: "蝈总"
TocOpen: false\
hidemeta: false
comments: false
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

# SA/VC中文字幕修改简易教程

有人问这个怎么做，B站有UP专门搞这个，但是也为了方便以后可能有别的作者继续玩中配MOD，我简要说一下VC和SA的中文字幕怎么改。这个流程只适用于无名汉化，别的汉化组件我没找到开源的构建工具。

## 一、提取字幕文本到TXT格式

无名汉化组件里有一个wm_xxchs.gxt文件，用了特殊的编码存储字幕文本，需要先转换到txt格式才能读写。

使用慕黑制作的中文gxt提取工具，打开这个gxt文件，即可在当前目录下生成对应的txt文件。

![image-20231211183935095](1.png)

打开这个txt文件，即可修改其中的内容。

**注意观察“~”、“=”这些符号是控制字符，不能写到字幕文本中，否则会出错。**

**如果写入了字库中没有的字，例如“蝈”、“蠕”，游戏中不会显示这些字。**

![image-20231211184022591](2.png)

## 二、构建新的GXT文件

无名汉化为VC、SA准备了各自的GXT构建工具（VCGXTBuilder、SAGXTBuilder），我事先用他们的源码构建了对应的可执行程序，诸位就不用去github下载源码了。将修改后的wm_xxchs.txt文件放到和exe工具同一目录下，双击exe工具即可构建出新的gxt文件，然后替换原gxt文件，就OK了。

## 三、工具下载链接

链接：https://pan.baidu.com/s/1khTuYqf_vyCDLQCQn44pEQ?pwd=0829 
提取码：0829

## 四、源码

exe程序基于以下源码构建：
GXT构建：https://github.com/WMHHZ/VC.SA.Plugin
GXT提取：https://github.com/Lzh102938/III.VC.SAGXTExtracter
