---
title: Xxl-job安装简述
date: 2025-07-30 08:00:00
tags:
  - 教程
published: true
abbrlink: 10209
---

Xxl-job的安装应该是很简单，只是有些地方需要注意一下

<!-- more -->

## pull镜像

```bash
docker run -di -e PARAMS="--spring.datasource.url=jdbc:mysql://192.168.252.135:3306/xxl_job?allowPublicKeyRetrieval=true&Unicode=true&characterEncoding=UTF-8&autoReconnect=true&serverTimezone=Asia/Shanghai --spring.datasource.username=root --spring.datasource.password=mysql@14321 --xxl.job.accessToken=archives" \
-p 8080:8080 \
-v /home/verlif/xxljob:/data/applogs \
--name xxl-job \
--privileged=true \
xuxueli/xxl-job-admin:2.4.0
```

### 参数说明

| 参数                         | 值说明           |
|----------------------------|---------------|
| spring.datasource.url      | 数据库jdbcurl地址  |
| spring.datasource.username | 数据库连接名称       |
| spring.datasource.password | 数据库连接密码       |
| xxl.job.accessToken        | xxljob任务token |

## web端访问

web端访问地址为：`http://<ip>:<port>/xxl-job-admin`

默认用户名为`admin`

默认密码为`123456`

### corn表达式错误

换个浏览器即可解决。

## java配置

```yaml
xxl:
  job:
    executor:
      address: # 本机地址，为空则自动获取
      appname: archives # 在xxljob中注册的执行器名称
      ip: # 本机IP
      logpath: /data/applogs/xxl-job/jobhandler # 日志目录
      logretentiondays: -1 # 日志保留时间
      port: 9999 # 本机执行端口
    admin:
      addresses: http://192.168.252.135:8080/xxl-job-admin # xxljob web访问地址，通常是 http://<ip>:<port>/xxl-job-admin
      accessToken: archives # xxljob token，与上文中的xxl.job.accessToken对应
```