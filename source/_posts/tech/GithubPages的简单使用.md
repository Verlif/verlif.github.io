---
title: GithubPages的简单使用
tags:
  - Github
  - 教程
published: true
cover: /images/MZNTLD8XI.png
banner: /images/MZNTLD8XI.png
abbrlink: 22690
date: 2024-01-20 15:09:53
category:
	- 技术
---
**Github**为每一个仓库都提供了**Pages**页面用于网页展示，这些**Pages**一般都用来作为**项目介绍**或是**文档展示**。

**Github**也为账号提供了**Pages**，只需要新建一个名为` 账户名.github.io`的**公开**仓库（例如`verlif.github.io`）皆可（访问地址就是` 账户名.github.io`或是关联的域名）。

![个人仓库](/images/1705737624918.png)

## 开启项目Pages

1. 首先仓库需要**公开**。
2. 进入仓库**设置**，选择**Pages**标签。

    ![Pages设置](/images/1705734821749.png)

3. 选择从分支部署（选择`none`则关闭），每个仓库只能选择一个**分支**作为**Pages**展示分支。
4. 选择页面根目录，**Pages**会在选择的分支下的该目录中（非子目录）的`index.html`、`index.md`或是`readme.md`作为首页文件。
5. （可选）填写**Pages**关联域名。

    ![设置部署信息](/images/1705735195903.png)

6. 查看构建进度，在项目的**Actions**中会自动生成**Pages**构建**工作流**，在这里可以查看**构建进度**（可能存在延时，若没有构建信息请等候一点时间）。

    ![Pages构建进度](/images/1705735723828.png)

## 查看Pages页面

在构建完成后就可以通过独立网址来访问部署的**Pages**了（若没有进行域名关联，则网址为：`https://verlif.github.io/socket-point`）。

![](/images/1705736000693.png)

网站地址构成为：

`https://` +` 账户名` +` .github.io/` + `仓库名称`

例如：`https://verlif.github.io/socket-point`。当然，如果你进行了域名绑定，则` 账户名 .github.io/`则会变成你的域名。

*绑定域名的前提是你填写的域名进行了CNAME设置，指向了`账户名 .github.io`。*

## 项目内链接

如果作为文档显示，必然需要在首页中加入链接。添加内部链接时，请使用相对路径，例如以下目录结构中：

```
├── index.md
├── person
│   └── 小明.md
```

`index.md`中跳转到`小明.md`的话，只需要链接`[小明](person/小明.md)`即可。

## 主题

默认生成的**Pages**页面是非常简洁的，想要更好看页面效果可以使用自己的`index.html`，也可以使用**Jekyll**的主题。

这里是[官方文档](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/adding-a-theme-to-your-github-pages-site-using-jekyll)，你也可以简单地在配置文件中更改主题名称。

1. 在仓库**Pages**的**选择分支**下的**根目录**下新建`_config.yml`文件（已存在则可以进行更改）。例如我这里的**Pages**设定就是在`master`分支下的项目`root`路径，就直接在项目根目录下新建`_config.yml`文件。

    ![_config.yml](/images/1705737113783.png)

2. 填写Github支持的**Jekyll主题**，支持的[主题列表](https://pages.github.com/themes/)。例如我这里选择的是[Slate主题](https://github.com/pages-themes/slate?tab=readme-ov-file)。
3. 点击右上角的提交按钮，提交文件更新。

    ![提交文件更新](/images/1705737377749.png)

4. 随后Github会进行部署工作流的运行，等待完成即可访问新的Pages页面。

    ![等待部署](/images/1705737436566.png)

    ![新主题效果](/images/1705737489580.png)

5. 完成。
