---
title: docsify使用简述
tags:
  - 教程
  - docsify
published: true
cover: /images/pPYRw-k3u.png
banner: /images/pPYRw-k3u.png
abbrlink: 56659
date: 2024-01-30 17:01:09
category:
	- 技术
---
之前一直想找一个文档表现工具，类似于语雀这种能方便显示目录层级和标题的在线文档管理器。网络上开源的大致分为两类：

- 管理平台，允许注册登录的开放式文档管理站点。
- 文档导航管理，由文档文件生成站点。

首先，管理平台对我来说太大了，我并不需要进行用户管理，并且这类平台是需要数据库存储文档数据的，这表示我需要一个服务器才可以部署。

其次，我一般喜欢文档为主体的内容管理工具，也就是文档由我自己管理，工具只负责将我管理的文档进行展示就可以了。

基于以上两点，我就找到了[docsify](https://docsify.js.org/)。

## 介绍

**docsify**基于文档内容形成站点，但是不同于Hexo这类博客网页生成器：

1. **docsify**只需要初始化一次，以后当文档更改时也不再需要使用命令或是按钮进行重新生成。
2. **docsify**只会在目录中生成`index.html`、`README.md`和一个`.nojekyll`文件，并不会生成网页目录。

因为**docsify**生成的`index.html`中引入了在线`js`，文档站点就是**由这个js动态生成**，所以这个`index.html`就是**站点首页**。

正因如此，在未来的文档更改后也不需要进行重新生成站点。

## 使用

使用实际上就是使用npm安装一个docsify客户端，然后用docsify初始化一下文档项目目录。

### 安装docsify-cli

```node
npm i docsify-cli -g
```

当然，如果你进度卡住了，我推荐你用这个镜像`http://mirrors.ustc.edu.cn`，就像这样设置一下：

```node
npm config set registry http://mirrors.ustc.edu.cn
```

### 初始化文档项目目录

一般推荐初始化`docs`目录，当然你也可以选择其他目录或是直接在当前目录初始化。

```NODE
docsify init docs
```

`init`后面的就是需要初始化的文档根目录，没有就是当前目录。

完成初始化后，指定目录下就会出现`index.html`、`README.md`和`.nojekyll`文件，他们的作用分别是：

- `index.html` - 首页配置，例如插件、样式、布局等。
- `README.md` - 首页文件，更改此文件就可以修改首页内容。
- `.nojekyll` - 用来告诉**Github**不需要使用**jekyll**进行构建。

![目录结构](/images/1706666928851.png)

*`.obsidian`是**obsidian**生成的，与**docsify**无关。*

值得注意的是，你直接访问`index.html`是不行的，你需要在此目录下使用进行**http**或是**https**环境搭建访问才有效果。

另外，如果想要将文档部署在**GitHubPage**上的化，`README.md`文件名必须是**大写**，否则会出现**404**。

### 预览

预览可以使用`docsify serve docs`，这里的`docs`与`init`后的参数是一样的作用，表示了文档根目录。你也可以先`cd docs`，然后`docsify serve`，效果是一样的。

服务启动后就可以通过访问`3000`端口来预览页面效果。

![效果预览](/images/1706666985379.png)

### 文档编写

就像正常`markdown`文档编写一样。在文档间跳转时使用**相对路径**即可实现页面间跳转。

## 总述

**docsify**很适合作为文档或是教程的生成工具，并且动态生成的机制也非常适合初始化后直接丢给文档编写人。

另外非常推荐使用**obsidian**进行搭配使用，**obsidian**只会检测`md`文件，这使得你在**docsify**管理的目录下编辑文档时，在文件列表中不会出现与内容无关的文件干扰。

## 参考资料

- [沉默王二 · 入坑 docsify，一款神奇的文档生成利器！](https://juejin.cn/post/6897123103583764488)