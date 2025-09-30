---
title: Verlif的地下室
tags: []
published: true
abbrlink: 62384
date: 2024-01-19 23:24:01
---
# Verlif的地下室

## 说明

这里是Verlif的地下室，里面可能乱糟糟的，什么都有。 
不过这些都不重要，有趣最重要。

在Verlif的仓库中，提供的开源工具与开源组件（以下称为“组件”）包括了：

* `Spring Boot`组件
* `Java`一般组件
* 工具&玩具

这些都可以在`GitHub`中找到，组件允许通过`Maven`或是`Gradle`等的构建工具将这些小东西添加到自己的项目中。

## 设计原则

对于组件的设计，我个人遵循的是低设计与高扩展原则。当然，这也只是我的设想，执行程度与我的代码能力正相关。  
特征包括了：

* 只做基础功能。不会有复合功能类似于的工具箱这类的依赖在组件中出现。
* 与业务无关。这点也是最重要的，所有的组件都必须轻松兼容绝大部分项目。
* 低依赖。尽量不使用三方依赖，这点主要是减少组件体量与提供功能拓展。
* 自定义化。每个组件都允许便捷地修改核心逻辑来配置自己的业务。
* 小而精。我个人有点代码洁癖，不太喜欢大而全的组件，所以给出的组件一般都是单功能的。

## 总列表

### Spring Boot 组件

* 全局异常处理 [exception-spring-boot-starter](https://github.com/Verlif/exception-spring-boot-starter)
  * 全局异常处理的组件化实现。
  * 不需要再在一个advice中写所有的异常处理了，现在只需要通过实现处理接口，异常处理器就会将对应的异常交由这个处理器处理。
* 接口限制器 [limit-spring-boot-starter](https://github.com/Verlif/limit-spring-boot-starter)
  * 只需要一个注解即可对接口进行访问控制。
  * 不同的接口允许不同的限制器来管理。
* 基础日志与接口日志服务 [logging-spring-boot-starter](https://github.com/Verlif/logging-spring-boot-starter)
  * 一个注解完成接口日志，一个接口管理基础日志服务。
  * 可配置的接口日志的处理。
* 接口权限服务 [permission-spring-boot-starter](https://github.com/Verlif/permission-spring-boot-starter)
  * 一个注解实现对接口的访问权限控制。
  * 与业务无关。适用于任何项目，无论是否有数据库，无论用户数据结构。
* 定时任务服务 [task-spring-boot-starter](https://github.com/Verlif/task-spring-boot-starter)
  * 通过注解完成定时任务。
  * 动态添加或关闭定时任务。
  * 可配置的定时任务名单。
* 文件管理系统 [file-spring-boot-starter](https://github.com/Verlif/file-spring-boot-starter)
  * 字节流或是Base64类型的文件上传与下载。
  * 文件的分页查询。
* 接口等幂性实现 [norepeat-spring-boot-starter](https://github.com/Verlif/norepeat-spring-boot-starter)
  * 针对接口的重复提交数据进行过滤，可以自定义重复判定。

### Java 组件

* 简单指令生成器 [just-simmand](https://github.com/Verlif/just-simmand)
  * 将任意的实例对象转换为一个指令组，其中的指令就是其拥有的公共方法。
  * 就像输入指令一样调用对象方法。
* Jackson序列化脱敏 [jackson-sensible](https://github.com/Verlif/jackson-sensible)
  * 使用便利的Jackson序列化的数据脱敏。
  * 支持几乎所有的数据类型（通过处理选择器中的脱敏处理器来实现，若内置的数据类型不足，可自行添加）。
* 参数解析器 [param-parser](https://github.com/Verlif/param-parser)
  * 将String类型转换为其他数据类型的解析器。
  * 为反射或是其他组件提供数据转换支持。
* Jar文件加载器 [loader-jar](https://github.com/Verlif/loader-jar)
  * 将Jar文件中的指定类的实例或其实现类提取出来。
  * 像是动态模组化的功能就可以使用，类似于游戏打mod。
* ~~Socket消息交互 [socket-core](https://github.com/Verlif/socket-core)~~
  * ~~内置了Server与Client用于简单的信息交互。~~
  * 现已被 [socket-point](https://github.com/Verlif/socket-point)代替
* Socket端点 [socket-point](https://github.com/Verlif/socket-point)
  * 更简单的方式进行socket交互
  * 更高的拓展性与多种监听，支持信息加密、连接过滤、限时连接等自定义特性。
* Socket指令交互 [socket-command](https://github.com/Verlif/socket-command)
  * 组合`socket-core`与`loader-jar`的一个多端指令系统
  * Server可以加载数个Jar文件作为指令集，并能在运行时再添加或关闭指令。
* 文本变量解析器 [vars-parser](https://github.com/Verlif/vars-parser)
  * 超轻量级的变量解析器，用于对文本中的格式化变量，例如`@{vars}`进行处理。
  * 比`replaceAll`好用多了。
* 数据生成工具 [mock-data](https://github.com/Verlif/mockdata)
  * 就像new对象一样，生成带有假数据的对象。
  * 用来做测试、填充数据库等的非常方便，不必再手动写假数据来测试了。
* html解析器 [html-parser](https://github.com/Verlif/html-parser)
  * 非常简易的HTML内容解析器，能链式定位或是搜索标签节点，并获取内容。
  * 可以作为爬虫的组件，非常轻量化。
* 行命令解析器 [cmdline-parser](https://github.com/Verlif/cmdline-parser)
  * 超轻量的行命令解析器，用来解析类似于`--username Verlif`这类格式的命令。
* ~~简单语言包 [easy-language](https://github.com/Verlif/easy-language)~~
  * ~~与`ResourceBundle`类似地通过语言文件来进行本地化处理。~~
  * ~~支持多文件夹与语言包追加。~~
  * 已被 [easy-dict](https://github.com/Verlif/easy-dict) 替代。
* 简单字典 [easy-dict](https://github.com/Verlif/easy-dict)
  * 支持多渠道字典，例如同时从文件、缓存、数据库、接口等载入字典。
  * 简单易用，只需专注字典查询实现即可。
* 简单文件处理 [easy-file](https://github.com/Verlif/easy-file)
  * 文件搜索、级联删除、级联复制、Base64处理、字符串方式读写等的工具。
* 简单停表 [stopwatch](https://github.com/Verlif/stopwatch)
  * 用于记录时间的简单停表。
* Markdown文档构造器 [MarkDone](https://github.com/Verlif/mark-done)
  * 用于输出Markdown文档，就像输出Word文档一样。
  * 很适合用于批量输出内容，比如爬虫输出或是文档下载。
* 对象对比器 [object-comparator](https://github.com/Verlif/object-comparator)
  * 用于做数据版本对比，比如name改变了、age变为了null等等。
  * 不再需要对对象的每个字段手动对比了，并且支持不同类的对象对比。

### 工具&玩具

* Markdown管理器 [NoHtml](https://github.com/Verlif/NoHtml)
  * 自动构建markdown文档的管理目录导航，像访问网页一样访问markdown文档。
* Link指令框架 [Linkmand](https://github.com/Verlif/LinkmandServer)
  * 基于 [socket-command](https://github.com/Verlif/socket-command) 的一个socket远程指令架构
  * 搭建了内置指令，允许客户端消息转发、基础身份认证等。
* Just-data [Just-data](https://github.com/Verlif/just-data)
  * 基于Spring Boot的接口生成服务，使用SQL语句与变量参数来自动生成后台访问接口（非代码生成）
  * 包括了登录、接口权限与文件管理，提供相对完整的应用服务。
  * 不需要二次开发，而是直接运行的jar程序。不需要改动代码，只需要配置即可使用。
* MockApi [mockapi](https://github.com/Verlif/mockapi)
  * 基于Spring Boot 2.6.14的虚拟接口生成服务，只需要一个注解即可为接口生成另一个对应的虚拟接口。
  * 虚拟接口能通过原始接口的访问方法直接访问，并返回原始接口应该返回的数据结构，其中填充了随机数据。
  * 简单来说就是为接口文档增加了返回结果功能，便于在文档期提供数据服务。

## 版本说明

组件的依赖版本基于多方面因素确定，非常主观。一般情况下分为以下类型

|  前缀   | 说明                    |
|:-----:|:----------------------|
| test  | 用于测试，其结构、功能、用途等都可能会变动 |
| beta  | 确定了组件的功能与结构，但未经功能测验   |
| alpha | 完成了一般的功能测验，但稳定性未知     |
|   无   | 发布版，稳定性尚可             |

## 关于ISSUE

如果您对组件有任何的疑问，可以在对应的仓库中添加`issue`。  
如果您有一些想要分享的想法，可以在本仓库中添加`issue`。

## 写在最后

这些组件完全由自己的兴趣为基础开发的，所以需求、设计可能与实际项目上不匹配，这些都会在后续的更新中慢慢优化。  
没有讨论群、没有二维码、没有官网。
