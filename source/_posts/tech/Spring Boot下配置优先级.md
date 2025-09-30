---
title: Spring Boot下配置优先级
tags:
  - SpringBoot
published: true
cover: /images/MvrLNnuWM.png
banner: /images/MvrLNnuWM.png
abbrlink: 34255
date: 2024-01-22 15:18:15
category:
	- 技术
---
开发**Spring Boot**项目的时候，配置参数是必要的，但是为了配合不同的运行环境与三方服务，我们就会创建多个配置文件。那么如何准确地在不同环境下拿到对应的配置就需要先了解配置的优先级。

本文测试环境为：`JDK17`、`Spring Boot 3.2.2`
测试方式为：注入`spring.application.name`名称结果

## 配置入口

**Spring Boot**的配置入口有以下三个：

- **启动参数**
- **application.***
- **bootstrap.yml**

*`Nacos`等的配置中心作为独立的服务，并不算在本文讨论范围内。*

当`application.*`与`bootstrap.yml`目录层级相同的情况下，配置优先级顺序为：`启动参数` > `application.*` > `bootstrap.yml`。

设计`启动参数`优先级最高的目的，是为了让开发者将项目打包后，仍可以在不修改文件的情况下完成参数修改，方便适配不同的运行环境。

## 配置优先级逻辑

配置的优先级其实是按照覆盖和补全逻辑走的，**后加载的会覆盖补全已有配置**，这表示**加载顺序越靠后优先级越高**。

## application.*配置优先级

**Spring Boot**项目`application.*`配置存放的地方有三个：

1. 项目代码**根目录**下
2. **resources**目录下
3. 打包**jar文件同级目录**下

这里的配置优先级是越外层，优先级越高。这表示存放目录的优先级为：

<center> <strong>jar包目录</strong>  >  <strong>项目目录</strong>  >  <strong>resources目录</strong> </center>

---

而在`jar包目录`与`项目目录`下，**Spring Boot**也会优先读取`config`目录下的文件，这表示文件优先级从高到底依次为：

1. jar包同级**config**目录
2. jar包**同级**目录
3. 项目内**config**目录
4. 项目**根目录**
5. **resources**目录

---

这里用图例简单说明一下，数字代表优先级（1最高），其中`config`下的`application.yml`的优先级最高并会覆盖其他三个配置参数。↓

![简单说明](/images/1705978576427.png)

### 额外说明

对于`application.yml`与`application.properties`两个后缀的配置来说，`properties`的优先级会更高

## 应用场景

最常见的场景就是多人协作开发。多人协作的项目总会遇到运行环境不相同的情况，例如本地redis、数据库地址、运行端口号等等，此时肯定不能使用同一个配置文件，必然会有个人的配置文件（启动参数并不是很推荐，主要是不便于修改和查看）。

那么这个配置文件推荐存放在`项目根目录`，优点是不需要更改启动参数，并且其他人提交代码也不会影响当前的个人配置。↓

![根目录下](/images/1705979569942.png)

## 参考资料

1. [huaihkiss · spring boot启动无法获取spring.application.name问题](https://blog.csdn.net/huaihkiss/article/details/116895434)
2. [恒宇少年 · 配置文件的加载顺序以及优先级覆盖](https://cloud.tencent.com/developer/article/1603233)