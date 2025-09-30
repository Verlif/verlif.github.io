---
abbrlink: 1
title: Hexo的简单部署与Obsidian的配合使用
date: 2024-04-26 21:07:48
cover: /images/hexo_obsidian_githubpages/hexo.png
banner: /images/hexo_obsidian_githubpages/hexo.png
tags:
	- 教程
	- Hexo
	- Obsidian
category:
	- 技术
---
从安装Hexo到与Obsidian配合完成一整套的个人博客编写，以及GithubPages的发布。
<!-- more -->

## 简要说明

[Hexo](https://hexo.io/zh-cn/)是一个命令行式的静态博客生成工具。选择**Hexo**的原因就是主题多，拓展性高。

> 快速、简洁且高效的博客框架
> *来自Hexo官网的Slogen*

[Obsidian](https://obsidian.md/)是一个轻量级写作软件。选择**Obsidian**的原因是项目式管理，对**markdown**文件书写方便且配置同步方便。

> Sharpen your thinking.
> *来自Obsidian官网的Slogen*

本文的目的是帮助作者从零开始搭建自己的个人博客系统，目标方式是由**Obsidian**编写内容，通过**Hexo**生成静态网页，发布到**GithubPages**托管站点完成全网访问。

*本文不会详细介绍软件安装过程和部分软件的使用方法，需要作者有一定的软件基础。*

## Hexo安装与使用

### Hexo的安装

1. Node.js环境

	首先，**Hexo**是基于**Node.js**编写的，所以需要电脑中有**Node**环境。作者可以通过指令`npm version`检查是否已安装**Node**以及**Node.js**的版本。
	
	如果没有可以从 [Node官网](https://nodejs.org/en) 点击**Download**下载并安装。

2. Hexo安装

	一般可以直接通过以下命令进行全局安装：
	
	```shell
	npm install -g hexo
    ```
    
	当然，官方也提供了简单的说明：[建站 | Hexo](https://hexo.io/zh-cn/docs/setup)。
	

### Hexo的使用

#### 初始化项目目录

通过以下命令来新建`blog`目录，并将此目录进行**Hexo**结构初始化。当前，这里的`blog`也可以使用绝对路径进行替换（下文中将以`blog`目录举例）:

```shell
hexo init blog
```

需要注意的是，在`init`的过程中，**Hexo**会从**GitHub**的仓库中下载初始化文件，所以**GitHub**的连接状态会影响`init`的速度甚至是成功结果。

![hexo初始化](/images/hexo_obsidian_githubpages/hexo_init.png)

*（后续的**根目录**就是指的这里的`blog`目录）*

#### 启动服务

进入第一步初始化好的目录，在此打开命令行，并输入以下命令（这里的`s`表示**server**，使用了指令缩写，后续的指令同样都使用了缩写。全称指令请参考 [官方文档](https://hexo.io/zh-cn/docs/commands)）。

```shell
hexo s
```

![hexo启动服务](/images/hexo_obsidian_githubpages/hexo_s.png)

当出现`Hexo is running`时表示服务已开启，此时通过浏览器访问[http://127.0.0.1:4000](http://127.0.0.1:4000/)即可访问此时构建的站点。可以看到此时的站点只有一个初始化的文章。

![hexo的默认页面](/images/hexo_obsidian_githubpages/hexo_default_page.png)

如需要停止服务，在控制台**按住Ctrl**，随后**按下C键**即可停止服务。此时可以进行构建或重新启动服务。

#### 编写第一篇文章

通常我们可以通过**Hexo**的指令来进行新增文章的操作，比如以下指令。当然，除非你开了第二个控制台，否则你需要先停止之前启动的服务才能进行这些指令操作。

```shell
hexo n "我的第一篇文章"
```

![通过Hexo命令新建文章](/images/hexo_obsidian_githubpages/hexo_new_post.png)

此时在项目根目录下的`\source\_posts`中就可以找到我们新建的`我的第一篇文章.md`文件，我们就可以通过自己喜欢的编辑器来编辑这篇文章了（**Hexo**当然不提供编辑器的功能了）。

![编辑文章](/images/hexo_obsidian_githubpages/edit_first_post.png)

编辑完成后，我们需要通过以下命令来清除之前的构建并重新编译文件。之所以需要`hexo clean`来清除构建是避免残留无用的文件。

```shell
hexo clean & hexo g
```

![重新构建目录](/images/hexo_obsidian_githubpages/hexo_clean_gen.png)

构建完毕后，再次通过以下指令来启动服务：

```shell
hexo s
```

此时我们再打开网页或是刷新网页就会是这样的：

![更新后的网页](/images/hexo_obsidian_githubpages/hexo_first_page.png)

为了后续更方便得进行文章编写与本地预览，我们一般可以这样使用指令：

```shell
hexo clean & hexo g & hexo s
```

#### 管理文章

从[前面的操作](#编写第一篇文章)中我们可以看出，`\source\_posts`就是我们的文章目录，我们就可以在这里对文章进行新增和删除。

这里，我推荐大家使用**Obsidian**来进行写作，不仅是编写方便，并且在适配度和管理上面很切合博客编写。下面开始介绍**Obsidian**。

## Obsidian的安装与使用

### Obsidian的使用

*本文主要介绍文章的书写，所以**Obsidian**不作特别细致的讲解，想要深入了解的作者可以自行在搜索引擎中搜索。*

#### 安装Obsidian

从官方[下载地址](https://obsidian.md/download)中下载并安装**Obsidian**。

#### 打开项目目录

打开**Obsidian**，并选择 **【打开本地仓库】** ，并选择项目目录下的`source`目录打开。

![打开项目目录](/images/hexo_obsidian_githubpages/obsidian_open.png)

如果选错了也可以从左下角的 **【打开其他仓库】** 图标重新打开项目。打开目录后，页面应该是这样的：

![初次打开文章目录](/images/hexo_obsidian_githubpages/obsidian_first_page.png)

#### 设置（可选）

**Obsidian**由于便利性的缘故，并不是严格按照**markdown**格式进行编写或显示的，所以对于常用**markdown**文档的作者来说可以进行编辑设置避免编写混乱。

1. 设置 - 编辑器 - 默认编辑模式 **源码模式**
2. 设置 - 编辑器 - 显示 - 缩减栏宽 **关闭**
3. 设置 - 编辑器 - 显示 - 严格换行 **开启**
4. 设置 - 文件与链接 - 使用Wiki链接 **关闭**
5. 设置 - 文件与链接 - 检测所有类型文件 **关闭**

#### 编辑文章

**Obsidian**左侧是目录，右侧是内容。在左侧选择了文章后，在右侧可以进行编辑。

![Obsidian编辑文章](/images/hexo_obsidian_githubpages/obsidian_edit.png)

*眼尖的小朋友已经看到了文章顶部的配置区域了，具体的配置可以看这里 [Front-matter | Hexo](https://hexo.io/zh-cn/docs/front-matter)*

#### 使用模板

在使用**Obsidian**时，非常便携性的一点就是可以通过模板的方式创建文章。

##### 新建模板

1. 在左侧目录中增加`templates`目录。
2. 在`templates`目录下新增文件`post`。
3. 编辑`post`模板文件。
4. 在`设置 - 模板 - 模板文件夹位置`中设置目录为`templates`。

这里推荐一个简单的模板：

```markdown
---
title: {{title}}
date: '{{date}} {{time}}'
tags: []
---

<!-- more -->


```

##### 使用模板

模板只能在已有的文章中使用，也就是，要先新建一个文章文件，然后点击左侧的【插入模板】图标并选择`post`模板，随后就可以看到模板的效果了。

#### 说明

有必要说明一下，**obsidian**在进行换行时使用的是**LF**规范，而**Windows**下默认的是**CRLF**规范，这就导致了在一些文章在被**obsidian**打开后会被Git标记为已修改，即使你什么都没有编辑过。

所以是将所有文章重新用**LF**规范格式化一次，或是谨慎使用**obsidian**打开**CRLF**换行的文章，抑或是推送时手动检查并回退格式，就看作者怎么考虑了。

具体可以看这篇文章[CRLF和LF的差异 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/380574688)

## 网站配置

文章当然很重要，但是网站也是门面，我们肯定要对它装饰装饰。

### 主题

第一步自然是先选一个顺眼的主题装上。从[这里](https://hexo.io/themes/)进入官方列出的主题列表。找到喜欢的主题，里面都有详细的安装介绍。

一般都是两个步骤：

1. 安装，包括但不限于通过`npm`安装或是从**GitHub**中下载主题文件到项目目录下的`themes`中。
2. 修改`_config.yaml`文件中的`theme`值，这个值对应的就是`theme`下主题的**文件夹名称**。

### 网站信息

网站信息大部分都在`_config.yaml`中，可以从[这里](https://hexo.io/zh-cn/docs/configuration)查看所有的配置参数。

常用的就是这些：

| 参数名      | 参数说明   | 参数举例               |
| -------- | ------ | ------------------ |
| title    | 网站标题   | Verlif的小站          |
| keywords | 网站关键词  | [博客, 文章]           |
| author   | 网站作者名称 | Verlif             |
| url      | 个人站点地址 | https://verlif.top |

### 网站图标

网站的图标需要一个名为`favicon.ico`的图标文件，图标文件可以从很多的图标网站下载（例如[阿里矢量图标库](https://www.iconfont.cn/)）或是自己画一个图片文件转成`ico`文件。

然后将这个文件放在项目根目录下的`source`目录下，也就是和`_post`目录同级。

## 网站部署

网站的部署其实有很多的方式，比如通过**Nginx**映射编译目录，或是通过**GithubPage**进行部署。

这两个方式都是将`hexo g`生成的`public`目录进行部署访问。这里只介绍**GithubPage**的部署方式。

### 安装Git

要部署到**GithubPage**就需要使用**Git**来提交文件到**Github**的仓库。

**Git**的下载地址在[这里](https://git-scm.com/download)。

### 注册Github账号

在[Github](https://github.com/)网站中点击右上角的 **【Sign up】** 按钮，随后按照网站指示进行注册。其中会用到个人邮箱，这里用国内邮箱也是可以的。

### 开启GithubPage

看这篇[文章](GithubPages的简单使用.md)。

### 本地密钥添加

添加密钥的目的是为了能有权限将文件提交到自己的Github仓库中。

1. [生成密钥](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
2. [添加密钥](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

### 本地deploy测试

Github环境准备好后，我们就可以开始部署网站了。

首先，我们需要现在本地进行deploy测试。这里我们就要继续修改`_config.yaml`文件，直接划到最后的找到`deploy`参数，就像这样配置：

![修改deploy参数](/images/hexo_obsidian_githubpages/config_deploy.png)

这里的`{{TOKEN}}`需要替换成自己的Github账号生成的**TOKEN**值。

后面的地址填写自己的**GithubPage**地址（地址结构就是`github.com/{{账户名}}/{{账户名}}.github.io.git`）。

那么如何能生成**TOKEN**呢？其实也非常简单，按照下图的方式就可以生成。

![生成TOKEN](/images/hexo_obsidian_githubpages/github_token.png)

这里是步骤解析：

1. 点击右上角的【头像】展开选项，选中【SETTINGS】。
2. 划至下侧的【Developer settings】。
3. 选择左侧的【Personal access tokens】分类，点击【Tokens (classic)】。
4. 点击右侧的【Generate new token】。
5. 选择【Generate new token (classic)】。
6. 填写【Note】，并将【Expiration】设置为【No expiration】。
7. 在【Select scopes】中勾选【repo】。
8. 最后划到最后点击【Generate token】生成**token**，此时会跳转页面并在页面中显示出**token**，注意，此**token**只会显示这一次，请将其复制下来，并替换之前地址参数中的`{{TOKEN}}`。

在地址参数修改好之后，通过以下指令进行部署，也就是将`public`目录提交到仓库中。提交成功后，就可以访问`https://{{账户名}}.github.io`来查看自己的网站了。

```shell
hexo d
```

## 自动化部署（可选）

本来通过`hexo d`就可以事项部署了，但是这个只能将编译好的网站部署，源数据（项目文件）只会保留在本地。想要数据迁移或备份时只能通过文件夹复制或是打包传递，有些许麻烦。所以我们需要将项目文件也通过**Github**保存管理。

此时我们就会管理两个仓库，一个是部署仓库（`{{账户名}}.github.io`），一个是项目仓库，这样当我们每次更新完文章后，既需要`hexo d`部署网站，有需要`git push`项目仓库，也有些麻烦，所以我们就用到了**GithubAction**来帮助我们部署，我们就只需要管理项目仓库即可。

### 新建项目仓库

我们这里在**Github**中再新建一个用来存放项目源文件（也就是根目录）的仓库，假设这个仓库名为**hexo-blog**，以下称为**项目仓库**。

新建的仓库用**private**（私有）仓库即可，**不推荐**初始化`readme`文件，这样方便我们直接`pull`。

### 本地初始化Git仓库

在项目**根目录**使用以下指令来创建本地仓库：

```shell
git init
```

### 设置远程仓库

在项目**根目录**使用以下来将新建的项目仓库设置成远程仓库（注意替换{{账户名}}为自己的账户名）：

```shell
git remote add origin https://github.com/{{账户名}}/hexo-blog.git
```

### 设置构建Action

使用**Action**来让**Github**在我们每次提交的时候自动部署到`{{账户名}}.github.io`。

实际上，使用**GithubAction**的方法非常简单，就是在项目**根目录**下新增`/.github/workflows/pages.yml`文件，其中`/.github/workflows`是两层文件夹，而`pages.yml`的名字可以自己拟定。

也可以通过**Github**页面进行配置，这里不做赘述。

这里我给一个我用的配置（参考资料[老猫-Leo · 【Hexo自动部署】优雅的使用 Github Actions 进行 Hexo 静态博客的持续集成与部署](https://cloud.tencent.com/developer/article/2369534)）：

```yaml
name: MyPageAction # 脚本 workflow 名称

on:
  push:
    branches: [main, master] # 当监测 main,master 的 push
    paths: # 监测所有 source 目录下的文件变动，所有 yml,json 后缀文件的变动。
      - '*.json'
      - '**.yml'
      - '**/source/**'

jobs:
  blog: # 任务名称
    timeout-minutes: 30 # 设置 30 分钟超时
    runs-on: ubuntu-latest # 指定最新 ubuntu 系统
    steps:
      - uses: actions/checkout@v2 # 拉取仓库代码
      - uses: actions/setup-node@v2 # 设置 node.js 环境
      - name: Cache node_modules # 缓存 node_modules，提高编译速度，毕竟每月只有 2000 分钟。
        uses: actions/cache@v2 # 亲测 Github 服务器编译速度比我自己电脑都快，如果每次构建按5分钟计算，我们每个月可以免费部署 400 次，Github yyds！！！
        env:
          cache-name: cache-node-modules
        with:
          path: ~/.npm
          key: ${{ runner.os }}-build-${{ env.cache-name }}-${{ hashFiles('**/package-lock.json') }}
          restore-keys: |
            ${{ runner.os }}-build-${{ env.cache-name }}-
            ${{ runner.os }}-build-
            ${{ runner.os }}-
      - name: Init Node.js # 安装源代码所需插件
        run: |
          npm install
          echo "init node successful"
      - name: Install Hexo-cli # 安装 Hexo
        run: |
          npm install -g hexo-cli --save
          echo "install hexo successful"
      - name: Build Blog # 编译创建静态博客文件
        run: |
          hexo clean
          hexo g
          echo "build blog successful"
      - name: "Deploy DoubleAm's Blog" # 设置 git 信息并推送静态博客文件
        run: |
          git config --global user.name "username" # 改成自己的账户名
          git config --global user.email "email" # 改成自己的邮箱
          hexo deploy

      - run: echo "Deploy Successful!"
```

这个**Action**表示，当我们的项目推送时，如果配置或是资源目录有更新的话，就会自动为我们在`{{账户名}}.github.io`进行部署。

从文件内容就可以看出，这里其实就是使用的本地指令来推送的，所以当后续使用了插件时，主要也要在这个配置文件中同是添加安装指令。

### 推送项目

推送就是使用**Git**将本地的项目文件推送到远程仓库，总共分为两步：

1. 添加新文件

    ```shell
    git add .
    ```

2. 提交到本地仓库

    ```shell
    git commit -m "更新说明"
    ```

3. 推送到远程仓库

    ```shell
    git push
    ```

这里在`git push`的时候可能会失败，在配置（包括ssh密钥配置）正确的情况下，通常是因为网络错误而失败，这里就需要多推送几次才能成功。

### 查看Action以及部署页面

当推送成功时，我们在项目仓库的**Action**记录中就可以看到工作流记录。

![Action记录](/images/hexo_obsidian_githubpages/github_action.png)

但你看到**绿色的√**就表示成功了。此时再去到`{{账户名}}.github.io`仓库，同样可以在`Action`中看到**Github**自带的**page-build**的工作流记录。

![PageAction记录](/images/hexo_obsidian_githubpages/github_page_action.png)

至此就已经成功了，可以访问`https://{{账户名}}.github.io`试试。

## 更多操作

更多的操作这里不做介绍，只是告诉作者还有的可能性。

- 添加文章评论。
- 添加画廊（图册）。
- 添加音乐播放器。
- 增加站点统计功能。
- 多层级站点管理。
- ……

## 注意事项

### 图片问题

虽然**Hexo**提供了`post_asset_folder`参数，让作者在创建文章的时候同时创建一个**同名**文件夹用来存放这篇文章的资源文件。
但是对于才使用的作者，我还是推荐更通用的方式，就是在`source`目录下创建`images`文件夹，手动管理这些图片。

只需要在文章中通过`/images/`的**相对路径**来添加图片，当然，你也可以在`images`目录下使用多层目录来管理不同类型或不同文章的图片。

### 部署后导致自定义域名失效

只需要在`source`目录下增加一个`CNAME`无后缀文件，其中的内容就是自定义域名，例如`verlif.top`。

参考资料：[桌子LZT · 解决 Hexo 部署 Github Pages 自定义域名失效的问题(即使已添加 CNAME）](https://blog.csdn.net/weixin_41747528/article/details/102772937)
