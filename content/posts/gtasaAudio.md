---
title: "GtasaAudio"
date: 2023-07-02T18:26:02+08:00
draft: false
author: "蝈总"
TocOpen: false\
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

# 史上最详细GTASA音频修改教程

本教程是《史上最详细GTASA修改教程》关于音频部分的展开详述，作者Cyber蝈蝈总（蝈总）。

![微信图片_20230517180113](1.jpg)

友利奈绪镇楼

依然说明一下，本教程**只针对PC老版**，虫豸版、安卓和IOS我概不了解也不打算做。

## 音频结构

圣安地列斯的音频，存储在`audios`文件夹，`audios`文件夹里又有`CONFIG`、`SFX`和`streams`文件夹:

`CONFIG`：声音配置文件，记录了SFX和streams里音频的信息

`SFX`：声音、音效文件，例如人物语音、武器车辆环境音效、任务对白

`streams`：音乐文件，例如电台音乐、过场动画、主题曲

### CONFIG

![image-20230702162309508](2.jpg)

如果是涉及音频文件的替换（而不是挂载），这些文件也会修改，因为音频大小发生改变，offset偏移值之类的东西也会改变。

### SFX

![image-20230702162446767](3.jpg)

`FEET`：脚步声或其他

`GENRL`：主要是车辆引擎

`PAIN_A`：CJ和NPC的惨叫声，把这个改成二次元老婆的娇喘，配合人物MOD老带劲了，别问我怎么知道的

`SCRIPT`：非过场动画的任务对白

`SPC_EA`：警察消防救护这些公共组织的NPC的语音

`SPC_FA`：特殊NPC的语音

`SPC_GA`：大部分NPC的语音

`SPC_NA`：帮派NPC和任务人物的语音

`SPC_PA`：CJ的语音

每个文件都是特殊编码的，解包之后会看到每个文件其实又分了许多个bank，具体每个bank里是什么内容，可以参看这两个链接：（这两个网站本身也很有用，如果愿意挖坟的话，可以挖到很多有用的信息）

> http://www.zazmahall.de/ZAZGTASANATORIUM/GTASA_SFX_Directory.htm#SCRIPT
>
> https://gtaforums.com/topic/923407-gta-san-andreas-sounds-list/

### streams

![image-20230702163516739](4.jpg)

都是与音乐有关的。俩字母的那些都是电台音乐，但是并不是完整的电台音乐，都被做了切片。

`BEATS`：开场曲、过关音效等，包括开局汤普尼驾车送CJ去巴拉斯地盘的过场对白。我真想不明白这段过场为什么放在BEATS里而不是CUTSCENE，害我找了老久（可能是因为这个动画并不是过场动画而是实时演算）。

`CUTSCENE`：过场动画的音频，一共70多条，命名没有规则，具体哪段是哪个过场动画全靠自己找，诶嘿。

## 音频编辑

如果想编辑和替换原有的游戏音频，这三样必不可少：modloader挂载器、SAAT声音替换工具、audacity音频编辑软件

### SAAT声音替换工具

全名 San Andreas Audio Toolkit，可以对SA进行声音的提取和导入。这里需要说明一件事儿，就是导入和导出的路径，还有游戏目录路径，都最好不要包含中文，否则可能失效。

一般的操作流程是，用这个工具把SA的音频进行提取，对提取出来的音频进行内容上的修改，然后再用这个工具导入到游戏里。或者，如果只修改SFX里的音频，修改音频之后可以将其放到MOD挂载器中，不需要覆盖游戏原文件。

### modloader挂载器

不多赘述，把需要替换的文件，按照目录结构理顺了，放在modloader文件夹就完事儿。SFX音频文件同理。

例如我的中配MOD的挂载内容如下：（看目录结构）![image-20230702173559164](5.jpg)

这里重点说明一件事儿：modloader不能挂载streams里面的文件，这个还是要靠SAAT进行封装。

### audacity音频编辑

这个不一定要用audacity，但是音频编辑软件一定要足够专业，能够进行bit级别的编辑。特别注意一件事情，就是你自己制作的替换音频，格式必须与原游戏对应的音频**完全一样**。采样率、数据率、时长，必须分毫不差，否则很容易出问题，即使没出问题，也建议中午做，因为早晚会出事。尤其是streams里的文件。

**技巧**：如果你用audacity，你可以先把原音频拖入，然后将自己做的音频也拖进来，把原音频前99.9%的内容扣掉，然后把自己的音频截成合适的长度，拖进去原音频空下的部分，然后删掉自己音频的轨道，然后导出为对应格式，就OK了。这样可以保证以上指标与原音频完全一致。图示过程如下：

第一步，拖入原音频

![image-20230702174353274](6.jpg)

第二步，拖入自己的音频，这里已经可以看到两条音轨采样率不一样，所以必须统一格式

![image-20230702174442427](7.jpg)

第三步，删掉原音频前99.9%的部分（是为了保证总长度不变）

![image-20230702174614148](C:/Users/78728/AppData/Roaming/Typora/typora-user-images/image-20230702174614148.png)

第四步，将自己的音频截成合适的长度，正好塞进原音频空下的部分

![image-20230702174704285](C:/Users/78728/AppData/Roaming/Typora/typora-user-images/image-20230702174704285.png)

第五步，删去自己的音频的轨道，然后导出为对应的格式(SFX都是wav单音轨，streams都是ogg双音轨)

![image-20230702174801700](C:/Users/78728/AppData/Roaming/Typora/typora-user-images/image-20230702174801700.png)

参考链接：https://gtaforums.com/topic/494176-sa-how-to-replace-anyall-sounds/

## MOD制作

如果想把你自己DIY的音频制作成MOD，要看你是替换了streams还是SFX，如果是SFX，那只提供modloader里需要挂载的音频即可；如果还替换了streams，那你在使用SAAT导入的时候实际上还修改了CONFIG里文件的内容，你最后的MOD里一定要带上你那里的CONFIG文件夹。

## 关于AI

看了这么多，咦，咋没有半点AI的东西？AI只负责制作你自己的音频，我这个教程，教的是你做好自己的音频后，应该怎么导入游戏。AI怎么玩，可以参考B站上SOVITS相关教程，我这里不重复讲了。我训练好的AI模型会选择合适的方式提供给愿意合作的朋友们，感谢大家的支持！