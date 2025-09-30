---
title: RabbitMQ的二三事
tags:
  - 二三事
  - RabbitMQ
published: true
abbrlink: 53515
date: 2022-09-11 09:59:06
category:
	- 技术
---
消息队列这个东西的确是好用。

## 连接问题

我这里需要使用`Spring Boot`来连接`RabbitMQ`，可以使用`spring-boot-starter-amqp`，我也确实是用的这个组件。

这里我在默认账户（__guest__）下创建了一个`vhost`，命名为`helloworld`，然后在此处创建了一个队列，叫`queue`。那么我要怎样连接呢，很简单：

1. 在pom中创建依赖

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-amqp</artifactId>
    </dependency>
    ```
   
2. 在application配置中配置如下参数：

    ```yaml
    spring:
      rabbitmq:
        host: 192.168.80.130  // 消息队列访问地址
        port: 5672            // 消息队列访问端口
        username: guest       // 访问账户
        password: guest       // 访问账户对应的密码
        virtual-host: /helloworld   // 连接的vhost名称
    ```

看起来很简单，我为什么还要记录呢？因为在`virtual-host`这里，我一开始忘写`/`了，就报`未找到vhost`错误。