---
title: Stellar主题使用
tags: [hexo,stellar,博客]
categories: [博客搭建]
cover: /images/jt/blog/alma.png
poster:
  topic: Stellar主题一些简单使用
  headline: Stellar主题使用
date: 2024-05-16 17:40:17
description:
---

## 评论插件

### Beaudar

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