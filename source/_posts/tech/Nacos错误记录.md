---
title: Nacos错误记录
tags:
  - Nacos
published: true
abbrlink: 63017
date: 2023-11-27 10:23:24
category:
	- 技术
---
## 连接

对于`SpringBoot`项目，连接`nacos`需要在`pom`配置中配置上以下依赖：

```xml
<dependency>
  <groupId>com.alibaba.cloud</groupId>
  <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```

并在配置文件`bootstrap.yml`中增加`nacos`服务器配置：

```yaml
spring:
  application:
    name: nacos-test # 当前项目的应用名，需要与nacos服务器中的配置对应
  cloud:
    nacos:
      config:
        file-extension: yml # 当前项目在nacos上的配置名后缀，没有后缀则不填
        server-addr: # nacos访问地址
        namespace: # nacos配置命名空间ID
        enabled: true
        username: # nacos账号
        password: # nacos密码
```

## 错误记录

### nacos未生效

常见的问题如下：

1. `bootstrap.yml`配置错误
   1. 项目名与`file-extension`是否与nacos服务器的配置对应
   2. `spring.cloud.nacos.config.enabled`设置成了`false`
   3. 用户名密码配置错误
   4. 命名空间ID配置错误
2. nacos中的配置错误
   1. 配置的**yaml**格式但出现了内容错误，导致配置失效