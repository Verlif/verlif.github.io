---
title: Java的一些奇技淫巧
tags: []
published: false
abbrlink: 2694
date: 2024-04-23 15:16:11
category:
	- 技术
---
## 集合类型初始化

```java
    private static final Set<String> SET new HashSet<String>() {{
            add("a");
            add("b");
            add("c");
    }};
```

这玩意看着奇怪，其实就是一个匿名内部类，两个大括号中，外层的是类定义括号，里面的是初始化代码块。

## 