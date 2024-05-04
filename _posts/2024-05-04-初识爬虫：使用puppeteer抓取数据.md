---
layout: single
title:  "初识爬虫：使用puppeteer抓取数据"
excerpt: 使用puppeteer抓取数据
categories:
- 教程
tags: 
- puppeteer
- 爬虫
- 中文
- MacOs
---

{% include toc title = "目录" %}

## 什么是爬虫：
**让我们通过ai快速认识一下这个概念：** 
> 爬虫，又称为网页蜘蛛或网络机器人，是一种按照一定规则，自动抓取万维网信息的程序或脚本。它们的主要目的是从互联网上获取数据，包括但不限于网页内容、图片、视频等。爬虫可以帮助用户快速获取大量的网络数据，特别是在数据量大、手动输入过于繁琐的情况下，这些工具能够极大地提高数据采集的效率和准确性。

## 为什么要使用puppeteer？
puppeteer是一款强大的网页自动化工具，它可以自动执行任务，并获取对于网页数据，因此可以用它作为爬虫来使用。谷歌官方的版本是通过js调用的，但是它在`python`、`ruby`、`go`上都有对应的移植版本。相比于老牌的scrapy框架，它的优势是在使用动态加载的当下，前端呈现的内容是由复杂的javascript控制的，对于混淆的程序，还需要去逆向分析很久。puppeteer在牺牲效率的情况下，提供了一种对新手更友好的爬虫思路。

## 在macos上安装puppeteer

### 1. 安装node
我尝试使用的是javascript版本的puppeteer。因此需要先确保有node环境。 进入官网下载[安装包](https://nodejs.org/en/)，系统会自动配置环境。
安装完成后，输入`node -v`，确认是否安装成功

### 2. 初始化puppeteer
1. 新建文件夹，输入`npm init`, 安装成功后会生成一个package.json的文件。
2. 输入`npm i puppeteer`进行安装

> 在实际操作的时候遇到了报错：`Error: Could not find Chromium`
> 这是因为puppeteer需要依赖Chromium，不确定是网络的问题还是平台的问题，导致Chromium包没有被成功下载。关于这个问题，网上有很多讨论。大部分都是建议手动执行命令，重新安装对应依赖：
> ```
> cd ./node_modules/puppeteer
> npm run install
> ```
> 不过这个方法对我无效,查阅资料后我选择了另一种解决方法：
> 1. `export PUPPETEER_SKIP_CHROMIUM_DOWNLOAD=true` //跳过使用puppeteer来下载Chromium包的步骤
> 2. `export PUPPETEER_EXECUTABLE_PATH = [your Chromium path]` 或在`/.bashrc`中永久设置路径 //手动指定Chromium位置，这里我使用了`brew install --cask chromium` 去下载包，路径位于`'/Applications/Google Chrome.app/Contents/MacOS/Google Chrome'`。
> 同时，也可以在js文件中加入这个路径，[教程请戳](https://juejin.cn/ >  post/6844903756857868301)。

### 3. 调试并运行你的第一个demo
1. 在当前目录下新建名为`demo.js`的文件，并写入如下内容：

```javascript
const puppeteer = require('puppeteer');

(async () => {
  const browser = await puppeteer.launch({headless:false});
  const page = await browser.newPage();
  await page.goto('https://example.com');
  await page.screenshot({path: 'example.png'});

  await browser.close();
})();
```

2. 使用`node demo.js`命令运行这个文件
3. 如果一切正常，你会看到程序启动了一个窗口，并马上将其关闭，在`demo.js`的同级目录下，会出现一张名为`example.png`的截图

## 3. puppeteer初实践
在这里，我打算使用puppteer抓取东大某部门教授及他们的研究方向。接下来，我将一一介绍我是如何做到的。
首先，打开你想使用puppter爬取的网站，选中你想抓取的内容，右键-选择检查，右侧会出现网站的源码，你选中的部分将以灰色高亮，右键-copy-copy selector。

![selector](/assets/images/selector.png)

让我们分析一下复制到的内容：  
```
#free > table > tbody:nth-child(1) > tr:nth-child(2) > td:nth-child(1) > a
```

Selector是CSS规则的一部分，它可以让我们快速精确地选择元素。这个选择器的结构可以分解如下：
- #free：选择ID为free的元素。
- table：选择ID为free的元素下的直接子元素table。
- tbody:nth-child(1)：选择table元素下的第一个tbody元素。
- tr:nth-child(2)：选择tbody元素下的第二个tr元素。
- td:nth-child(1)：选择tr元素下的第一个td元素。
- a：选择td元素中的a元素。

也许你会想，这个选择器过滤出来的结果一定是唯一的吗，让我们使用devtools提供的终端来验证一下。
![selector2](/assets/images/selector1.png)

如图所示，使用命令`$$('#free > table > tbody:nth-child(1) > tr:nth-child(2) > td:nth-child(1) > a')`，确实只给我们返回了一个结构体。  
那么按照这个思路，我们删除一些结构，筛选出所有的名单。接下来使用命令`$$eval("table > tbody > tr > td > a", (links) => links.map((x) => x.innerText))` 试着检查一下返回值吧。  
这里我们用到了回调函数`(links) => links.map((x) => x.innerText)`。这是一个箭头函数，它接收一个参数 `links`，这个参数是一个包含所有匹配的` <a> `元素的数组。函数内部使用` .map() `方法遍历这个数组，对每个`<a> `元素调用 `.innerText` 属性，获取元素的文本内容。这个函数的返回值是一个新的数组，包含了所有`<a> `元素`.innerText` 属性的文本内容。接下来就是对结果的保存和写入了，这部分内容比较简单，在这里我贴出我的源码。

```javascript
const puppeteer = require('puppeteer');
const fs = require('fs');

(async () => {
  const browser = await puppeteer.launch({ headless: false });
  let courses = ["cs", "mi", "ipc", "ice", "m-i", "ci"];
  let data = [];
  let contents = [];

  for (let i = 0; i < courses.length; i++) {
    const page = await browser.newPage();
    let course = courses[i];
    await page.goto(`https://www.i.u-tokyo.ac.jp/edu/course/${course}/members.shtml`, { waitUntil: 'networkidle0' });
    await page.setDefaultTimeout(60000); // 增加超时时间
    let titles = await page.$$eval("table > tbody > tr > td > a", (links) => links.map((x) => x.innerText));
    let content = await page.$$eval("table > tbody > tr > td", (links) => links.map((x) => x.innerText));
    console.log(titles);
    console.log(content);
    data = data.concat(titles);
    data = data.concat(content);
  }

  fs.writeFile("data.json", JSON.stringify(data, null, '\t'), function (err) {
    if (err) {
      console.log(err);
    }
  });

  await browser.close();
})();
```

在这里，我还添加了`waitUntil: 'networkidle0' }`的配置选项，当网络连接保持空闲状态超过指定的时间（默认是500毫秒）时，Puppeteer 将认为页面已经加载完成。这可以避免资源未加载完引发的错误行为。同时增加超时时间，防止出现程序卡死。  
注意，此处有一些部门的名单无法输出，这是因为对应page的css样式不同，不过由于这只是一个简单的实验，也就不做细化了。