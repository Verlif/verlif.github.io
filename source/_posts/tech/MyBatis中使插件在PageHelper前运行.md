---
title: MyBatis中使插件在PageHelper前运行
tags: []
published: true
abbrlink: 32318
date: 2024-03-01 13:38:00
category:
	- 技术
---
## 概要

前情提要：

> 我们有两个项目**A**与**B**，两个项目单独开发，但实际上**A**是**B**中的一个功能，两者使用了相同的数据库结构。

需求：

> 我们需要将**B**和**A**项目进行对接，使用**B**项目替换**A**项目中的相同功能。

问题：

> **B**的所有表名与**A**的表名存在映射关系，且各不相同，例如在**A**中的`user`表对应了**B**中的`student`表。

方案：

> 在**B**中通过**MyBatis插件**的方式，将SQL中需要映射的表名改为**A**项目库的表名。

## 问题

本来方案构想是挺好的，但是因为在**B**项目中使用了**PageHelper**插件，我们自定义的SQL处理插件运行于**PageHelper**之后，而在`PageInterceptor.intercept`方法中，并没有使用`invocation.proceed()`，这导致SQL执行的流程中，**PageInterceptor**执行后就**不执行**自定义插件的方法了。

## 解决

目前最简单的办法就是让自定义插件在**PageHelper**之前运行。

那么已知插件运行顺序是栈式的，**后加载的插件先运行**，所以我们就需要将自定义插件的加载顺序滞后。

所以我们就需要手动控制加载顺序了。

### 网上的解决方案

在网上翻了半天，都是些没法用的方法，而且十个有九个都是抄来抄去的，垃圾。

另外就是网上的很多文章都提到了`PageHelperAutoConfiguration`，但是B项目中并没有找到相关文件，这里应该是依赖或是版本不同导致的。

直到我翻到知乎的一个回答，里面贴了一个掘金的链接，里面的标题是：[基于拦截器处理mybatis模糊查询中的‘%’、‘_’、‘\’特殊字符问题](https://juejin.cn/post/7291156890734985270)，我说咋搜不到呢。

最终的方案就是通过`ApplicationListener`的方式，将自定义拦截器置于插件队列尾部。完整代码如下：

```java
@Component
public class CustomerInterceptorRegister implements ApplicationListener<ContextRefreshedEvent> {
    @Autowired
    private List<SqlSessionFactory> sqlSessionFactoryList;
    @Autowired
    private ReplaceTableInterceptor interceptor;

    @Override
    public void onApplicationEvent(ContextRefreshedEvent event) {
        for (SqlSessionFactory sqlSessionFactory : sqlSessionFactoryList) {
            org.apache.ibatis.session.Configuration configuration = sqlSessionFactory.getConfiguration();
            // 防止多次调用时的重复添加
            if (configuration.getInterceptors().stream().noneMatch(i -> i == interceptor)) {
                configuration.addInterceptor(interceptor);
            }
        }
    }
}
```

这里是通过`ContextRefreshedEvent`事件进行触发的，这样再项目初始化完成后再加载自定义插件时，**PageHelper**就已经被加载了。