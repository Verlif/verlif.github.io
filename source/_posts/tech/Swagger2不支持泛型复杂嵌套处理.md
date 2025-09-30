---
title: Swagger2不支持泛型复杂嵌套处理
tags:
  - Java
  - SpringBoot
published: true
abbrlink: 920
date: 2023-03-30 10:13:36
category:
	- 技术
---
在**swagger2**中，默认情况下并不会支持泛型复杂嵌套，就像是`Map<String, List<Person>>`或是`List<Map<String, Object>>`，如果有这样的返回值的话，访问**swagger**页面就会在顶部出现异常信息。

为了解决这个问题，或是说为了自定义解析方式，**swagger**提供了**rule**配置，让开发者自行配置解析方式。

就像下面这样：

```java
docket.alternateTypeRules(
        AlternateTypeRules.newRule(
                typeResolver.resolve(List.class, typeResolver.resolve(Map.class, String.class, Object.class)),
                typeResolver.resolve(List.class, WildcardType.class), Ordered.HIGHEST_PRECEDENCE)
        );
```

上面这行表示将`List<Map<String, Object>>`转换成`List<WildcardType>`类型，去除了嵌套就可以解析了。

解决办法其实很简单，那为什么我要写出来呢？因为网上查的东西太烂了！！！
