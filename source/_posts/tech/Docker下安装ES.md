---
title: Docker下安装ES
tags:
  - 教程
published: true
abbrlink: 23819
---

Docker下安装ElasticSearch还是很简单的，只需要两步，第一步创建卷，第二部创建并运行容器

<!-- more -->

## 创建数据卷

创建数据的目的是为了避免权限问题

```bash
docker volume create es_data
docker volume create es_config
docker volume create es_plugins
```

## pull

```bash
docker run -d --name elasticsearch   -p 9200:9200 -p 9300:9300   -e "discovery.type=single-node"   -e "ES_JAVA_OPTS=-Xms1g -Xmx1g"   -e "xpack.security.enabled=false"   -v es_data:/usr/share/elasticsearch/data   -v es_config:/usr/share/elasticsearch/config   -v es_plugins:/usr/share/elasticsearch/plugins   swr.cn-north-4.myhuaweicloud.com/ddn-k8s/docker.io/library/elasticsearch:7.17.3
```