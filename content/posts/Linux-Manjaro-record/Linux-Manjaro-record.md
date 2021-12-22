---
title: "Linux/Manjaro 折腾记录"
description: "Liiiiiiiiiiinux!"
date: 2021-12-22T10:53:20+08:00
draft: true
categories: [技术]
tags: [Linux, Manjaro]
disableShare: true
cover:
    image: ""
    caption: ""
---

## 为什么选择Linux&Manjaro

当然是因为Linux很酷:check_mark:

当然正经地说，酷确实是一个原因。除此之外，另一个原因是我觉得Linux在各种方面都比Windows更适合程序员：

1. 一切皆文件。没有C盘D盘等盘符让命令行cd起来很顺手（我的Windows电脑现在都是格成一个盘，数据放OneDrive就好啦）；
2. 优秀的包管理方式。统一的命令行安装卸载方式让**我觉得**自己的电脑很干净，而Windows下各种`ProgramData``Program Files`（甚至路径里有一个空格！）`Program Files (x86)`还有注册表看的人头大。Windows下的Scoop和Choco？用起来总觉得治标不治本；
3. 高度自定义的外观。我对dock栏有一种莫名的执着，而Windows只能用它的资源管理器，第三方的dock总会有各种适配问题；
4. 越用Linux就越嫌弃Windows。个人经验。

那为什么不用Mac？因为穷。

当然Linux也有许多缺点，比如：

1. 不能打游戏！因此个人主力机肯定还得是Windows；
2. 驱动支持不好。比如我新买的Lenovo Yoga 13s，本来拿到第一件事就是想装个Linux，结果因为网卡比较新所以内核驱动不支持，联想官方放出的驱动也只有`.exe`的，加上触摸板用起来手感很奇怪，而且还有Office需求，因此随身轻薄本也得是Windows；

综上，我盯上了实验室的电脑：硬件旧，无Office需求，coding场合多，不打游戏，而且只有一块物理硬盘，简直完美适合。

至于操作系统的选择，首选KDE桌面环境（GNOME的扩展性感觉还是稍弱了，而且还需要gnome-tweak结合浏览器插件来管理桌面插件？感觉不够Unix哲学！而且KDE的Yakuake下拉终端真的香），试过Manjaro与Kubuntu，体验下来还是Manjaro 更胜一筹。

## 安装记录

*Todo*