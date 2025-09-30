---
title: 玩意儿
tags:
published: true
cover: /images/toys/cover.png
banner: /images/toys/cover.png
date: 2024-04-27 12:46:28
menu_id: toys
---

## [MockData](https://github.com/Verlif/mockdata)

生成随机数据或填充随机数据到对象中的工具，非常适合用来做测试或接口开发。

![数据展示](https://github.com/Verlif/mockdata/raw/master/docs/4.x/imgs/mockDebugger.png)

## [MockApi](https://github.com/Verlif/mockapi)

基于**SpringBootWeb**的接口生成框架，只需要一个注解或是非侵入注册即可自动生成对应格式的数据接口。

比如有这样一个接口：

```java
@GetMapping
public BaseResult<User> getById(Integer id) {
    return null;
}
```

访问在**MockApi**注册并生成的接口可以得到以下数据：

![数据对比](https://github.com/Verlif/mockapi/raw/master/docs/3.x/imgs/数据对比.png)

## [Windonly](https://github.com/Verlif/windonly)

简单的数据拖拽面板桌面应用，适用于频繁的复制粘贴。

![图示](https://github.com/Verlif/windonly/raw/master/docs/images/mainWindow.png)

## [OnePage](https://github.com/OnePageWeb/one-page)

> 一页 · ONE 是一个基于 Vue3 的纯前端个人主页生成器。
> 通过 组件 + 网格 的方式，你可以自由组合、定制并分享属于自己的页面。

在线访问：[one](https://one.verlif.top/)

![基础页面](https://github.com/OnePageWeb/one-page/raw/master/docs/images/screenshots/quick-config.png)