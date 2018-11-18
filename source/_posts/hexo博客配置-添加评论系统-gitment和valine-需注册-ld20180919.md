---
title: hexo博客配置-添加评论系统-gitment和valine-需注册
author: ledong
comments: true
tags:
  - Hexo
  - 坑
  - 技术构架
  - 个人博客
categories:
  - 技术构架
  - 个人博客
keywords: 'Hexo,gitment,valine,坑, 搭建个人博客'
description: 本文主要介绍如何在Hexo中搭建gitment和valine评论系统。
abbrlink: d196c9ad
date: 2018-09-19 12:00:00
---

> 本文所用的架构或思路参考该文：{% post_link hexo博客配置-主题管理与配置 %}

## 配置方案

安装参考如下即可——注意有坑，请继续往下看。这里LeanCloud的登录用github来注册登录即可。

[hexo部署github和gitment操作简单介绍](http://www.cnblogs.com/xcg-yg/p/8394022.html)

[object ProgressEvent](https://github.com/imsun/gitment/issues/170)

[hexo博客评论新神器——Valine](https://blog.csdn.net/esa_dsq/article/details/78626509)

[Valine -- 一款极简的评论系统](https://ioliu.cn/2017/add-valine-comments-to-your-blog/)

## 坑

使用gitment，要先在评论区登录github，否则显示`Error: Comments Not Initialized`(中文显示`评论不可用`之类)；登录后点击`Initialize Comments`。另外可能会报错`[object ProgressEvent]`或者`gh-oauth.imsun.net`链接不上。这是因为`gh-oauth.imsun.net`网站证书失效了！解决方法：

方案一：单独访问这个网站`https://gh-oauth.imsun.net/`，加入例外，允许浏览器访问。但这个方案，别人看你博客的人不一定知道要加，所以不好。

方案二：更改`node_modules/gitment/dist/gitment.js`中`https://gh-oauth.imsun.net`，直接改为请求 github 认证的接口`https://github.com/login/oauth/access_token`

参考：[imsun/gitment/issues#102](https://github.com/imsun/gitment/issues/102),[gitment评论模块接入hexo](https://github.com/jjeejj/jjeejj.github.io/issues/8)

## 使用双评论系统

此处用了gitment（稳定）和Valine（方便）。可以对`themes/hexo-theme-next/layout/_partials/comments.swig`的`if-else`语句稍微修改即可。我的改完后长这样：

{% include_code lang:swig ../themes/hexo-theme-next/layout/_partials/comments.swig %}

## 顺带添加LeanCloud文章阅读量统计（待验证是否可行）

就是在配置Valine的同时，创建一个class，名叫“Counter”，这样可以和Valine共用一个app了。


## 其他参考：

[Hexo（NexT 主题）评论系统哪个好？](https://www.zhihu.com/question/267598518)

[Hexo-NexT主题添加评论功能（来必力、Hypercomments、畅言、友言)](https://blog.csdn.net/qq_32454537/article/details/79482879)

