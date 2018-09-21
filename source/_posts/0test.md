---
title: 测试
author: ledong
date: 2999-12-30 12:00:00
comments: true
tags:
  - Hexo
  - docker
  - 技术构架
  - 个人博客
categories:
  - 技术构架
  - 个人博客
keywords: 'Hexo+coding, next, docker, 搭建个人博客'
description: 
abbrlink: 59cdbec3
---

## 测试

{% post_link hello-world 世界你好！ %}

{% post_path hello-world  %}

这是摘要(写了上面的description,这里就不起作用啦)

<!-- more -->

<!-- md hello-world.md -->

<!-- ---------------- -->

{% include_code _posts/hello-world.md %}

{% fold 点击显/隐内容 %}
hahah
hfhadfh
fadfh
{% endfold %}

## [测试Tag Plugins](https://hexo.io/docs/tag-plugins)

{% blockquote David Levithan, Wide Awake %}
Do not just seek happiness for yourself. Seek happiness for all. Through kindness. Through mercy.
{% endblockquote %}

{% blockquote @DevDocs https://twitter.com/devdocs/status/356095192085962752 %}
NEW: DevDocs now comes with syntax highlighting. http://devdocs.io
{% endblockquote %}

{% blockquote @DevDocs https://twitter.com/devdocs/status/356095192085962752 %}
NEW: DevDocs now comes with syntax highlighting. http://devdocs.io
{% endblockquote %}

{% raw %} 123 {% endraw %}
{% tabs %} 123 {% endtabs %}
{% centerquote %} 
123 
789
{% endcenterquote %}
{% note %} 
123 
{% subnote %} 
890 
76
{% endsubnote %}
456
{% endnote %}
{% fold 点击显/隐内容 %}
something you want to fold, include code block.
something you want to fold, include code block.
something you want to fold, include code block.
something you want to fold, include code block.
something you want to fold, include code block.
something you want to fold, include code block.
something you want to fold, include code block.
something you want to fold, include code block.
{% endfold %}