---
title: >-
  Python3中Django项目遇到UnicodeEncodeError: 'ascii' codec can't encode characters
  in ordinal not in range(128)
author: ledong
comments: true
tags:
  - 问题解决
  - Python3
  - Django
  - encode
categories:
  - 问题解决
description: >-
  在项目配置文件`settings.py`中加入代码`sys.stdout = codecs.getwriter("utf-8")(sys.stdout.detach())`来解决。
abbrlink: 6d93f12
date: 2018-11-16 12:00:00
keywords:
---

[toc]

## 问题描述

Python3中Django项目遇到`UnicodeEncodeError: 'ascii' codec can't encode characters in ordinal not in range(128)`

## 解决方案

在项目配置文件`settings.py`中加入如下代码：

```py
import sys
import codecs
sys.stdout = codecs.getwriter("utf-8")(sys.stdout.detach())
```

另外注意系统的语言配置，比如Ubuntu可采用：

```bash
export LANG="en_US.UTF-8" #或者C.UTF-8
```

在运行python命令前添加参数

```bash
PYTHONIOENCODING=utf-8 python test.py
```

该参数的解释可查看官方文档：
https://docs.python.org/3.6/using/cmdline.html#envvar-PYTHONIOENCODING

## 主要参考：

[Python3中遇到UnicodeEncodeError: 'ascii' codec can't encode characters in ordinal not in range(128)](https://blog.csdn.net/th_num/article/details/80685389)