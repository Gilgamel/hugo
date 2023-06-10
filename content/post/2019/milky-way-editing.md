---
title: "银河后期"
description: 
date: 2019-08-25T22:00:40-04:00
image: 
math:
license: 
categories:
tags:
- Lightroom
- 银河
hidden: false
comments: true
draft: false
summary:
---

抓住19年拍银河的小尾巴，跟特特还有海王去了 Toronto Barren 拍银河。

俗话说得好：拍摄一时爽，后期火葬场。

用来拍摄的单反是 long time ago 的60D，由于是APS-C画幅，所以拍摄时的思路是用 **高感 + 大光圈 + 慢快门** 尽可能将更多的星星拍摄进去。然而忽略了机子的问题，等看到RAW的时候发现噪点十分多，修起来真的是太难了。在找了好多个preset以后发现颜色出来都不大自然（至少比我目前修出来的还不自然），所以无法偷懒的自己开始不断 google 着。



用的参数是 3200 ISO + f2.8 + 30s，拍摄6张以后拼接在一起。原图大概是这个亚子的：

![BIBBB-2.jpg](https://i.loli.net/2019/08/27/NRu19LOZD7KmnAt.jpg)



## Camera RAW 基修

在LR里将6张图拼接导出以后，用 Camera RAW 打开了图片。由于在LR里已经预先将图片都进行了镜头校对，所以在 Camera RAW 里就省去这一步了。



直接对对比度、高光、阴影、白色、黑色进行修改，重点是将图片调灰

![image.png](https://i.loli.net/2019/08/27/g6XyOVDNb5RolhL.png)

**高光**

提高高光可以使银心更加明亮，

**阴影**

提高阴影可以使暗部提高一些，显示出细节

**黑色**

提高黑色一样可以显示出细节

**白色**

降低白色可以让图片整体亮度稍微压低一点

**对比度**

适当调低可以方便PS后期修整

除此以外，还需要适当增加清晰度以及自然饱和度，曝光度根据图片情况选择性加/不加。



### 降噪

将 Camera RAW 调到细节，然后开始降噪

注意蒙版需要稍微调大一点，颜色也需要降噪，否则会出现噪点过多的问题

![image.png](https://i.loli.net/2019/08/27/wa1cR4VgHOfMbtz.png)



## Photoshop 修饰

### 明暗修饰

#### 亮部修饰

 利用快捷键 **CTRL + ALT + 2** 选取高光部位。

这时画面上会出现闪闪发亮的就是高光部位，然后用调整层工具 **曲线** 进行修饰

由于我们现在选取了高光部位，所以操作仅仅会针对亮的部分进行。

通过拖动曲线，让亮的地方更亮，把比较灰的地方拉下来。

![image.png](https://i.loli.net/2019/08/27/2SFZaTUIGe7vuOq.png)



#### 暗部修饰

按着曲线部分的蒙版（曲线）选区，CTRL + ALT +2 选择高光部位后，然后在菜单栏选择 **反选**，接着再加曲线。

这时需要将暗部提亮，但是比较暗的地方需要继续压下去

![image.png](https://i.loli.net/2019/08/27/lypVeYR97vSEUhA.png)

#### 整体层次修整

在不选择高光部位的情况下，添加曲线，对整体明暗进行修饰

![image.png](https://i.loli.net/2019/08/27/jArR4msZYCFIoEi.png)

Ref：

> https://www.xinpianchang.com/a71314?from=ArticleList