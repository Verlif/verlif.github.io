---
title: SpringCloud学习记录
tags:
  - Java
  - SpringCloud
published: true
abbrlink: 2829
date: 2022-11-07 10:12:28
category:
	- 技术
---
最近一直在学`SpringCloud`，还挺好玩的，这里记录一下遇到的一些问题。

## `No instances available` 错误

首先，这个错误的本质是未找到对应的服务，比较明显的错误就是调用的服务名在注册中心中未能找到。
但是经过我的检测，在注册中心的服务列表中是有对应服务的，那问题出在哪里呢？
我重新查了一下，找到一篇文章 [Eureka出现No instances available for xxx的五种解决方案](https://blog.csdn.net/Kevinnsm/article/details/117117061) ，里面还是挺详细的，我对比了一下，我的确是把`spring-cloud-starter-netflix-eureka-client`和`spring-cloud-starter-netflix-ribbon`依赖都加上了。
在重新调整`pom`文件后，地址可以正确访问了，OK。
