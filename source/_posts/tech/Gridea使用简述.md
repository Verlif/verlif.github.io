---
title: Gridea使用简述
tags:
  - Gridea
  - 教程
published: true
cover: /images/NXQ1WtpiB.png
banner: /images/NXQ1WtpiB.png
abbrlink: 44429
date: 2024-01-21 02:46:28
category:
	- 技术
---
最近我不是在重新搞个人站点嘛，选型选了半天还是选择了Gridea。

## Gridea介绍

[Gridea](https://open.gridea.dev/)是一个界面化的静态网页生成器，与[Hexo](https://hexo.io/zh-cn/index.html)或是[Hugo](https://gohugo.io/)是同一类型的。

不过**Gridea**是界面化的，这表示它能够更方便地管理文章、标签等资源，不过也就缺失了类似于自动化或是脚本的功能。

![Gridea界面](/images/1705777112320.png)

- Markdown编辑
- 支持标签归类
- 支持多种主题（主题相比于Hexo算很少了，但是作为个人博客也算够了）
- 本地预览与**Github**推送

## 安装及配置

1. 从[下载页面](https://open.gridea.dev/#started)开始，选择对应的系统进行安装。
2. 安装后开始设置。

    设置远程配置，也就是站点的自动部署配置，这里以**GithubPages**举例。

    ![配置界面](/images/1705777732954.png)

    - 域名 - `Github账户名.github.io`（例如`verlif.github.io`）或是在**GithubPages**配置的**关联域名**（关于如何配置Github上的个人站点可以看[这篇文章](https://verlif.top/post/MZNTLD8XI/)）。
    - 仓库名称 - `Github账户名.github.io`，例如我的就是`verlif.github.io`。
    - 分支 - **Github**的**io仓库Pages配置**的分支。
    - 仓库用户名 - 就是使用的**Github**账户名。
    - 邮箱 - 账户配置的邮箱。
    - 令牌 - 用于**Gridea**进行提交的**Github权限令牌**，具体流程（点击Github头像进入`Settings` -> 最后一项`Developer Settings` -> `Personal access tokens` -> `Tokens` -> `Generate new token`）
  
        ![生成token](/images/1705778816731.png)

        这里只需要`repo`的所有权限即可，不需要多的权限，然后点击末尾的`Generate token`按钮即可生成。`Expiration`是**token有效期**，这里可以设置为`No expiration`无期限，否则`token`到期后需要重新生成配置一次。生成后会出现`token`内容，这里复制就可以了。

        ![token内容](/images/1705779108287.png)

        - CNAME - 解析别名，没有域名的话可以不填，否则就填写域名。
        - 代理 - 因为**GitHub**的DNS老是被污染，所有可以选择自己的代理进行仓库推送。

3. 点击左下角的`检测远程连接`按钮进行配置连接测试，~~连接测试通过~~点击右下角`保存`则配置完成。

## 添加文章并发布。

### 编辑文章。
        
点击主页右上角的`+`号添加新文章。↓

![编辑文章](/images/1705780099611.png)

在右侧的`设置图标`进入文章设置，包括常用的`标签`和`封面图`设置。↓

![设置文章属性](/images/1705780355707.png)

点击右上角的`绿色√`保存（`灰色√`表示存草稿，在部署时不会显示）↓

![保存文章](/images/1705780507698.png)

### 本地预览。

在首页的左下角点击`预览`按钮进行本地部署。↓

![预览](/images/1705780925811.png)

### 远程部署。
    
点击首页左下角`同步`按钮进行远程部署。同步完成后，即可通过对应域名访问。↓

![部署效果](/images/1705781422364.png)

### 选择主题并重新发布。

1. 进入`主题`选择已有的主题或是[`更多主题`](https://open.gridea.dev/themes)进行下载。↓
   
    ![主题设置](/images/1705781923664.png)

2. 主题的下载其实就是从**GitHub**上把主题项目拉取到主题目录下。
   
    从左下角的设置按钮中查看项目目录，主题就存在于`项目目录/themes`中。↓

    ![设置按钮](/images/1705782415725.png)

    ![Gridea源目录](/images/1705782503155.png)

3. 新下载的主题需要**重启Gridea**才可以选择。选择后重新**同步**即可。

## 多端编辑

这里的多端编辑指的是方便在不同的PC设备上进行编辑，因为**Gridea**本身并没有提供数据服务器，所以需要借助**Git仓库**直接同步**Gridea**应用数据。当然，你也可以直接把`Gridea目录`打包用云盘甚至U盘进行“同步”，不过这样明显很麻烦，不如**Git指令**方便。
   
1. 在**项目目录**中**初始化git**并提交到**Git仓库**。
2. 其他电脑安装**Gridea**软件。
3. 拉取项目到本地，并在**Gridea**设置**项目目录**为拉取的项目存放路径。这里需要注意的是因为原电脑安装的主题文件中可能有`.git`目录，导致可能项目目录在上传时不会同步部分主题，那么其他电脑在拉取时就需要手动重新下载该主题，否则编译会失败无法部署。

## 配置Gitalk评论

**Gitalk**的好处在于评论认证，借用**GitHub账号**作为评论账号，降低无意义的评论，但也增加了评论门槛，有得有失。如果需要引入其他评论系统，也可以按照其他评论的教程来引入，这里只介绍**Gitalk**。

在`远程` -> `评论配置`中，主要进行**Gitalk**的密钥配置。↓

![Gitalk配置](/images/1705833041100.png)

其中的`Client ID`和`Client Secret`都是在自己的**Github**中新建的应用，新建地址在[这里](https://github.com/settings/applications/new)。

像这样填写就OK了，`Homepage URL`和`Authorization callback URL`填写主页地址（例如`verlif.github.io`）。↓

![新建应用](/images/1705833390264.png)

新建完成之后，在这个应用设置中就可以新建密钥了，这一步没有难度不做说明。↓

![配置应用](/images/1705833537833.png)

推荐将此应用的**logo**提交一下，因为其他人在进行评价时，账号关联的是你建立的这个应用，显示的头像最好像是那么回事，不然别人都可能觉得是什么诈骗盗号网站。

不同的主题显示的评论样式也不尽相同，不过大差不差吧。↓

![评论展示](/images/1705833849755.png)

以后的每篇文章的评论都会初始化为一个`issue`，这篇文章的评论都会在这一个`issue`内。↓

![Gitalk评论issue](/images/1705833914062.png)
