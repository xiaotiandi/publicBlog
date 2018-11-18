---
title: Hexo博客搭建-用hexo-coding-next搭建个人博客
author: ledong
comments: true
tags:
  - Hexo
  - docker
  - git
  - coding
  - 技术构架
  - 个人博客
categories:
  - 技术构架
  - 个人博客
keywords: 'Hexo, coding, next, docker,git, hexo+coding+next,Ubuntu,Linux, 搭建个人博客'
description: 
abbrlink: cb10f677
date: 2018-08-17 11:00:00
---

## 总体思路

- 原则上，Hexo的运行环境与博客目录分离。

- 具体来讲，用Docker方式安Hexo，配置Docker里的git环境并建立与coding的链接；在本机建立博客目录，并配置主题、写博客；最后，运行，可配置快捷方式（本文是Linux系统，在`.bashrc`添加），运行博客系统或者发布博客。

<!-- more -->

## 安装和运行Hexo

- 方案一：直接安装。参考[Hexo中文文档——安装](https://hexo.io/zh-cn/docs/)，不赘述。
- 方案二：采用Docker。即采用如下的`dokcerfile`创建docker。

{% fold 点击显/隐内容 %}

```dockerfile
## https://blog.csdn.net/u013076044/article/details/66289301
## https://hub.docker.com/_/node/

FROM node:10.9.0-jessie

MAINTAINER ledong <your@mail.com>

EXPOSE 4000

ENV REFRESHED_AT 2018-08-15
ENV HEXOWEBSITE=/opt/websiteTest

VOLUME [${HEXOWEBSITE}]
WORKDIR ${HEXOWEBSITE}

## https://segmentfault.com/q/1010000009487303?_ea=1946152
## https://blog.csdn.net/u014792304/article/details/78687859
## https://blog.csdn.net/hoshea_chx/article/details/78826689
RUN cd ${HEXOWEBSITE} &&\
    npm config set registry https://registry.npm.taobao.org &&\
    npm install -g hexo-cli

RUN git config --global user.email "your@mail.com" &&\
    git config --global user.name  "yourname" &&\
    ssh-keygen -t rsa -f /root/.ssh/id_rsa -P "" -C "your@mail.com" &&\
    cat /root/.ssh/id_rsa.pub

WORKDIR /opt/
```

{% endfold %}

## 建立一个博客目录并配`next`主题

先绑定`~/test/`目录的方式进入docker

```bash
docker run -it -p 4000:4000 --name myblog_hexo  -v ~/test/:/opt/website  myblog_hexo /bin/bash
```

在docker里的`/opt/website/`目录下，执行如下代码：

```bash
hexo i blog
cd ~/test/blog
git clone https://github.com/theme-next/hexo-theme-next themes/hexo-theme-next
```

这样在本地会有一个`~/test/blog/`文件夹，就是博客的主目录，修改其中的`_config.yml`更改主题

```yml
theme: hexo-theme-next
```

## 运行博客

先在Linux系统中的`~/.bashrc`中加入如下代码（注意，这里绑定了博客目录`~/test/blog/`到docker的`/opt/website`）

```bash
alias hbrun='docker stop myblog_hexo ; docker rm myblog_hexo ; docker run -it -p 4000:4000 --name myblog_hexo  -v ~/test/blog/:/opt/website  myblog_hexo /bin/bash  -c "ssh -T git@git.coding.net; ssh -T git@github.com; bash"'
alias hbs='docker stop myblog_hexo ; docker start myblog_hexo; docker exec -i myblog_hexo /bin/bash -c "cd /opt/website/; hexo clean; hexo g ; hexo s"'
alias hb2git='docker stop myblog_hexo ; docker start myblog_hexo; docker exec -i myblog_hexo /bin/bash -c "cd /opt/website/; hexo clean; hexo g ; hexo d"'
```

然后，运行`hbrun`命令用于进入docker。若在本机进入`~/test/blog/`目录，运行`hbs`即可本地发布博客，浏览器输入`http://localhost:4000/`查看。而`hb2git`命令的使用，看下一部分。

## 使用git管理Hexo

一开始的`dockerfile`代码中，因为该docker是第一次用git，因此`dockerfile`里一定要运行如下命令(第二条可不运行)：

```bash
git config --global user.email "your@email"
git config --global user.name "yourname"
```

在coding上导入公钥`/root/.ssh/id_rsa.pub`后，测试是否成功（如果后面使用过程中出现连接错误，这个测试可以解决问题）：

```bash
ssh -T git@git.coding.net
ssh -T git@github.com
```

接着在coding上注册一个项目`yourblog`，获取链接`git@git.coding.net:youname/yourblog.git`

之后，在主目录的`_config.yml`添加如下代码。注意有坑，主目录下`_config.yml`在配置deploy时要忽略掉.deploy_git文件夹，不然每次部署该目录由于递归存入会滚雪球增大。

```yml
deploy:
  # - type: git
  #   repository: git@github.com:youname/yourblog.git
  #   branch: master
  - type: git
    repository: git@git.coding.net:youname/yourblog.git
    branch: master
  - type: git
    repository: git@git.coding.net:youname/yourblog.git
    branch: hexo
    extend_dirs: /
    ignore_hidden: false
    ignore_pattern:
      public: .
      /: .deploy_git
```

然后，运行前面提到的`hb2git`命令，就可以把生成的网页目录`~/test/blog/public/`部署到coding项目的master分支；而整个博客项目`~/test/blog/`推送到hexo分支。

经过如此一番，就可以到coding上去部署自己网页啦。即打开浏览器，进入coding刚建立的项目`yourblog`，代码-pages服务，部署来源选择master，然后可以选择https访问。

另外，遇到换电脑时，只要Hexo已经安装好，下载hexo分支即可直接得到整个博客项目。

```bash
git clone -b hexo git@git.coding.net:youname/yourblog.git
```

**最后，为了管理主题方便，可用git来管理主题，请参考该文：{% post_link hexo博客配置-主题管理与配置 %}**

## 注意事项

next主题从5到6的版本更新要注意，许多lib不能用了。[从 NexT v5.1.x 更新](https://github.com/theme-next/hexo-theme-next/blob/master/docs/zh-CN/UPDATE-FROM-5.1.X.md)

## 参考

[Hexo中文文档——安装](https://hexo.io/zh-cn/docs/)

[基于docker的Hexo博客系统](https://blog.csdn.net/u013076044/article/details/66289301)

[使用coding和Hexo快速搭建博客](https://www.cnblogs.com/vzhiwen666/p/8656114.html)

[用Hexo + github搭建自己的博客 --- 再也不用羡慕别人了](https://blog.csdn.net/hoshea_chx/article/details/78826689)

[Hexo Next 四种主题](https://blog.csdn.net/acm_th/article/details/79974513)

[hexo的next主题个性化教程:打造炫酷网站](https://www.jianshu.com/p/f054333ac9e6)

[Hexo搭建个人博客--NexT主题优化-来自Alvabill](https://www.jianshu.com/p/1f8107a8778c)

- [如何实现博客的public和整个Hexo备份-看知乎上“张钊”的回答](https://www.zhihu.com/question/21193762)