---
title: C#打包更新设置
date: 2019-04-19 18:47:20
tags: C#
categories: C#
---
C#打包更新设置
<!--more-->
我的是Setup1.的General Information文件，如我左边圈住的有三个重要的项，如下图。

Product Version：你本次产品的版本号，每次发布新产品，这个的Version号，要增加一下。我上次的版本是1.02，所以这次我设置为1.03.

Product Code：这就是一个guid，相当于产品的唯一识别码，每一次发布新版本，__需要变更__，可点击该行右边的{...}按钮进行随机分配。

Upgrade Code：这个是产品的升级码，同一个产品共用一个升级码，这里要__保持不变__。就像所有的vs2010都用的同一个Upgrade Code，每此vs2010有update，它只是改的Product Code。

测试无效，仍在探索新方法

