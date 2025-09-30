---
title: 装饰你的Github个人首页
tags:
  - Github
  - 教程
published: true
cover: /images/P89aEzarh.png
banner: /images/P89aEzarh.png
abbrlink: 25841
date: 2024-01-20 14:36:58
category:
	- 技术
---
Github提供了用于装饰个人主页的仓库识别，只需要新建一个与**账户同名**的**公开**仓库（以下称为**展示仓库**），就可以让这个仓库中的`readme.md`显示在个人主页上。

## 基本设置

仓库`readme.md`文件：

![readme.md](/images/1705733225918.png)

效果示例：

![首页显示](/images/1705733206715.png)

## 更多效果

实际上还有更酷炫的界面，这就需要用到一些**html代码**了，一般这些代码由三方服务提供，这里介绍两个常用的代码。

### 数据徽章

例如[Shields.io](https://shields.io/)提供了一些动态数据徽章：

![Shields.io](/images/1705733595439.png)

### 打字机特效

常见的打字机特效例如这个：

[![Typing SVG](https://readme-typing-svg.demolab.com?font=Fira+Code&pause=1000&color=29F7DD&center=true&random=false&width=435&lines=%E8%A3%85%E9%A5%B0%E4%BD%A0%E7%9A%84Github%E4%B8%AA%E4%BA%BA%E9%A6%96%E9%A1%B5)](https://git.io/typing-svg)

[Readme Typing SVG](https://github.com/DenverCoder1/readme-typing-svg)就是其中一个服务提供者，你只需要从[demo站点](https://readme-typing-svg.demolab.com/demo/)进行创建然后复制代码即可完成。

![demo配置](/images/1705734192381.png)

## 总述

个人仓库的`readme.md`与普通仓库的`readme.md`是一样的，实际上，项目仓库`readme.md`可用的代码，展示仓库依然可用。更复杂的使用，可能就需要在服务提供商那里提供`token`或是进行`action`设置了。