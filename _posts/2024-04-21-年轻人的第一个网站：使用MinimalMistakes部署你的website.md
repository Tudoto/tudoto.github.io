---
layout: single
title:  "年轻人的第一个网站：使用Minimal Mistakes一键部署你的website"
excerpt: 介绍Minimal Mistakes的使用方法，以及书写语法
categories:
- 教程
tags: 
- Minimal Mistakes
- website
- 中文
- 写给QQ同学的教程
---

{% include toc title = "目录" %}

## 前言：
如果你是一个新手，且完全不想折腾，其实更好的选择是购买一个云服务器，三大云都提供wordpress的镜像。WordPress是一个以PHP和MySQL为平台的自由开源的博客软件和内容管理系统。它可以让用户在无需接触代码的情况下，通过可视化模块搭建网站(想想我们曾经装扮过的qq空间)，且内置模版很多，运维起来也很轻松。但是每年都需要续费，这对我来说是一笔不小的费用，遂转投github。

### 什么是github pages
在`github`的官方介绍中，是这么描述`github pages`的：
`GitHub Pages` 是一种静态站点托管服务，它直接从 `GitHub` 上的存储库获取 `HTML、CSS` 和 `JavaScript` 文件，可以选择通过构建过程运行文件，然后发布网站。您可以在 `GitHub` 的`github.io`域或您自己的自定义域上托管您的网站。

翻译过来就是，我们需要在`github`上创建一个库，`github`识别到你的库后，会帮你生成属于自己的网站，听起来是不是so easy？ 振作精神，接下来打开电脑，让我们开始正式搭建我们的网站吧。

### fork你喜欢的模版
首先，你需要一个github账号。然后在github上找到一个你喜欢的模版，点击fork，复制一份到你的仓库，仓库名为`username.github.io`，其中`username`替换为你的GitHub用户名。

静静等待几分钟，让github为你创建你的个人网站。一切顺利的话，你可以在`username.github.io`的网址发现你fork过来的网页已经可以进行访问了。

## Minimal Mistakes
本站是使用`Minimal Mistakes`搭建的。它是一个免费的`jellky`编写的主题。我们可以在这个基础上，通过修改小部分代码，实现自己的个人网站。在这一步，你可以通过github网页进行修改。当然，更好的方式是将代码clone到本地，使用git进行每次提交的管理。 todo：git教程

### Minimal Mistakes的基本架构
以下是官方示例(test目录下)的文件树：

├── Gemfile  
├── _config.yml  
├── _data  
│   ├── authors.yml  
│   └── navigation.yml  
├── _pages  
├── _pets  
├── _portfolio  
├── _posts  
├── _recipes  
└── assets  
│   └── images  
└── index.html  

文件夹的概述如下：

| 文件/目录 | 描述 |
| --- | --- |
| _config.yml | 存储配置数据。也包括你的个人信息。 |
| _layouts | 预置的网站模板。 |
| _posts | 你的博客内容。这些文件的命名约定很重要，必须遵循以下格式： YEAR-MONTH-DAY-title.MARKUP. |
| _data |  Jekyll 引擎将自动加载此目录中的所有数据文件（使用.yml、 .yaml、.json或格式.csv和 .tsv扩展名），其中navigation.yml 规定了导航栏的信息。 |
| _sass | 这些是可以导入到您的 sass 部分main.scss ，然后将其处理成单个样式表 main.css，该样式表定义您的站点要使用的样式。 |
| assets | 对文章中出现的其他资源(如图片)做统一管理。 |

在对整个框架有了一定了解后，我们可以开始着手进行修改了。
### 修改个人主页资料

打开`_config.yml`,对其中一些属性做修改：

| 属性 | 描述 |
| --- | --- |
| minimal_mistakes_skin | 设置背景样式，这理我选择的是dark |
| locale | 设置网页的主要语言 |
| title | 设置网站的名字|
| subtitle |  出现在网站题头下方的简洁标语 |
| name | 网站的作者 |
| description | 网站介绍 |

在`site Author`一栏中，你可以修改你的`profiler`。
完成以上这些，你就可以让这个主页属于你了。当然，在`_config.yaml`中，还支持站内搜索，读者评论等拓展功能，更多内容请参考[官方教程](https://mmistakes.github.io/minimal-mistakes/docs/configuration/).

### 修改导航栏
在`navigation.yml`中修改`main`的内容。`title`中填入你想要的标题名，在`_pages`中创建`md`文件，并把路径填入`url`中。如果一切都没有问题的话，在刷新网站后，你会看到你定义的导航栏信息

### 添加你的第一篇日志
在`_posts`目录下创建一篇`YEAR-MONTH-DAY-title.md`的文章，注意此处的日期很重要，`minimal mistakes`会根据时间自动为你的文章进行排序。
文章由两部分组成：  
一部分是由两条虚线组成的，其中包含了文章的布局、添加的tag等一系列信息。另一部分就是文章的本体啦。
以下是我整理的一些常见的关键字：

| 属性 | 描述 |
| --- | --- |
| title | 文章标题 |
| categories | 文章分类 |
| layout | 文章的布局|
| excerpt |  文章简介 |
| tags | tag |
| permalink | 为文章设置永久链接|
| last_modified_at | 上次修改的时间 |


## 其他尝试
### 打开搜索栏
位于`config.yml`中：
```yml
search                   : true # true, false (default)
search_full_content      : true # true, false (default)
search_provider          : lunr # lunr (default), algolia, google
lunr:
  search_within_pages    : true # true, false (default)
```

### 支持读者评论
同样位于`config.yml`中：
```yml
comments:
  provider               : disqus # false (default), "disqus", "discourse", "facebook", "staticman", "staticman_v2", "utterances", "giscus", "custom"
  disqus:
    shortname            : "yourshortname" # https://help.disqus.com/customer/portal/articles/466208-what-s-a-shortname-
```
`disqus`是一个第三方网站，需要注册一个账号，选择“I want to install Disqus on my site”，填写完相应信息后，会得到一个shortname，对应填入上述shortname参数中。

### 修改网站字体
位于` _reset.scss `中，将原本的
```yml
html {
  /* apply a natural box layout model to all elements */
  box-sizing: border-box;
  background-color: $background-color;
  font-size: 16px;

  @include breakpoint($medium) {
    font-size: 18px;
  }

  @include breakpoint($large) {
    font-size: 20px;
  }

  @include breakpoint($x-large) {
    font-size: 22px;
  }

  -webkit-text-size-adjust: 100%;
  -ms-text-size-adjust: 100%;
}
```
修改为：
```yml
html {
  /* apply a natural box layout model to all elements */
  box-sizing: border-box;
  background-color: $background-color;
  font-size: 16px;

  -webkit-text-size-adjust: 100%;
  -ms-text-size-adjust: 100%;
}
```
## 总结
经过上述努力，你应该已经掌握了搭建网站的基本技能。如果你还想掌握进阶的功能，可以阅读jekyll和minimal mistak的官网。如果你还有什么疑问和心得，欢迎在评论区和我交流。


## todo list
- markdown插入图片的体验真让人不爽，如何让每张图片大小都赏心悦目。插入图片必须放在assert下？。
- 支持中文英文切换