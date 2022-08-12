---
title: "使用PicGo+Gitee搭建线上图床"
date: 2022-08-12T11:27:18+08:00
draft: false

tags: ["hugo", "picgo", "gitee"]
categories: "hugo"
---

写博客难免用到图片，放到github仓库会很臃肿，每次pull下来会很慢。所以就上网找到了一个教程，使用PicGo + Gitee作为图床，简单记录一下，本篇文章就是使用这个方案写的。使用gitee的原因是国内速度快。

<!--more-->

## 一、安装PicGo

在 github 上找到 PicGo 的安装程序。我选择的是最新的[稳定版v2.3](https://github.com/Molunerfinn/PicGo/releases/tag/v2.3.0)

下载安装不必多说，安装完成后打开 PicGo，安装一个插件：

![安装Gitee-uploader插件](https://gitee.com/littleelves/images/raw/master/img/Snipaste_2022-08-12_11-32-56.png)

## 二、创建一个 gitee 仓库用来存储图片

新建仓库，现在 Gitee 不能直接设置为公开仓库，需要创建完成，绑定手机之后才能设置为公开的。

![新建仓库作为图床](https://gitee.com/littleelves/images/raw/master/img/Snipaste_2022-08-12_11-35-48.png)

## 三、设置PicGo的图床仓库

找到左侧的 `图床设置 > Gitee`，将刚才创建的仓库设置为图床。

![设置仓库为图床](https://gitee.com/littleelves/images/raw/master/img/Snipaste_2022-08-12_11-41-52.png)

|属性|说明|
|repo|仓库的作者/仓库名字，不知道的可以打开仓库，复制地址栏后面的链接|
|branch|分支，因为是单分支，所以是master|
|token|个人令牌，依次点击 `头像 -> 设置 -> 安全设置 -> 私人令牌` 生成，要记号，下次就要重新生成了|
|path|上传到仓库的哪个文件夹|

其余两个可以不用填，点击确定，设置为默认图床即可。

## 使用图床

点击左侧的上传区就可以上传图片了，下面可以选择生成图片链接的格式。

PicGo还可以从剪贴板上传图片，配合截图软件Snipaste非常好用。

上传完成后，链接会自动复制到剪贴板。

> 参考：[PicGo + Gitee(码云)实现markdown图床](https://zhuanlan.zhihu.com/p/102594554)