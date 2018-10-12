---
title: hexo博客配置-自建标签Bug解决方案
author: ledong
comments: true
tags:
  - Hexo
  - 代码风格
  - 技术构架
  - 个人博客
categories:
  - 技术构架
  - 个人博客
keywords: 'Hexo,tag,自建标签,渲染代码段Bug, 搭建个人博客'
description: 本文主要介绍如何在Hexo中解决自建标签渲染代码段Bug的问题。
abbrlink: dd56df28
date: 2018-09-20 12:00:00
---

> 本文所用的架构或思路参考该文：{% post_link hexo博客配置-主题管理与配置 %}

## 修复自建tag里写代码块，渲染成undefined的问题

只需添加文件`hexo-theme-next/scripts/custom/debugCustom.js`如下

{% include_code lang:js ../themes/hexo-theme-next/scripts/custom/debugCustom.js %}

## 参考

[参考1：Hexo添加代码块折叠](https://www.cnblogs.com/woshimrf/p/hexo-fold-block.html)

[参考2：Hexo自建标签渲染代码段Bug解决方案](https://www.oyohyee.com/post/Note/hexo_tag/)