---
wiki: hexo-stellar
title: Stellar主题使用
author: JT
tags: [hexo,stellar,博客]
categories: [博客搭建]
cover: /images/jt/blog/alma.png
banner: /images/jt/blog/alma.png
poster:
  topic: Stellar主题一些简单使用
  headline: Stellar主题使用
date: 2024-05-16 17:40:17
description:
layout: page
---

# 评论插件

## Beaudar

> Beaudar 是 Utterances 的中文版本，相比 Utterances 有更多的体验优化，可以按时间倒序排序

[Beaudar](https://beaudar.lipk.org/) 的配置方法很简单，创建一个仓库，在仓库中创建一个 [域名白名单文件](https://github.com/JiangTaot/blog-comments/blob/main/beaudar.json)，然后在 [此处](https://github.com/apps/beaudar) 授权安装即可

{% tabs active:2 align:center %}

<!-- tab stellar配置 -->

~~~yaml
# 评论插件安装
comments:
  service: beaudar
  beaudar:
    repo: JiangTaot/blog-comments
~~~

<!-- tab beaudar配置 -->
```json
{
  "origins": [
    "https://jiangtaot.github.io"
  ]
}
```
{% endtabs %}

其他插件请见：{% link https://xaoxuu.com/wiki/stellar/comments/  Stellar主题插件配置  icon:/images/jt/blog/alma.png %}



# 网站链接

{% tabs active:2 align:center %}

<!-- tab 演示效果 -->

{% link https://xaoxuu.com/wiki/stellar/tag-plugins/express/  Stellar主题外链卡片设置  icon:/images/jt/blog/alma.png %}

{% link https://xaoxuu.com/wiki/stellar/tag-plugins/express/  Stellar主题外链卡片设置  icon:/images/jt/blog/alma.png desc:true %}

<!-- tab 语法格式 -->

外链卡片语法格式：

~~~bash
{% link href [title] [icon:src] [desc:true/false] %}
~~~

参数含义：

~~~yaml
href: 链接
title: 可选，手动设置标题（为空时会自动抓取页面标题）
icon: 可选，手动设置图标（为空时会自动抓取页面图标）
desc: 可选，是否显示摘要描述，为true时将会显示页面描述
~~~

<!-- tab 写法示例 -->

~~~bash
无摘要
{% link https://xaoxuu.com/wiki/stellar/tag-plugins/express/  Stellar主题外链卡片设置  icon:/images/jt/blog/alma.png %}
有摘要
{% link https://xaoxuu.com/wiki/stellar/tag-plugins/express/  Stellar主题外链卡片设置  icon:/images/jt/blog/alma.png desc:true %}
~~~

{% endtabs %}




