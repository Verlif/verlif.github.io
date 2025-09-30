---
title: new Integer(128)
tags:
  - Java
published: true
abbrlink: 10087
date: 2022-03-01 10:07:59
category:
	- 技术
---
`Integer`本身是`int`的包装对象，是`Number`的子类。

## 问题

Java中的经典引用判断问题，除了`String`还有`Integer`。本来以为只会在面试题里遇到，结果真的在项目中遇到了。以下是代码表现：

```java
    Integer a = 28;
    Integer b = new Integer(28);
    Integer c = Integer.valueOf(28);
    Integer d = Integer.parseInt("28");

    System.out.println(a == b);
    System.out.println(a == c);
    System.out.println(a == d);

    Integer a2 = 128;
    Integer b2 = new Integer(128);
    Integer c2 = Integer.valueOf(128);
    Integer d2 = Integer.parseInt("128");

    System.out.println(a2 == b2);
    System.out.println(a2 == c2);
    System.out.println(a2 == d2);
```

通过不同的手段对`Integer`进行赋值，然后判断相同值的引用是否相同。

## 答案

首先对于看过源码的开发者应该知道答案：

```text
false
true
true
false
false
false
```

## 解释

因为在`Integer`中有一个缓存类`IntegerCache`，用来减少`Integer`对象的创建。不过毕竟默认的缓存也不能占用太多，否则就适得其反，所以Java中就给定了缓存区间 `-128~127`。
所以在`a`、`b`、`c`和`d`四个变量中，除了`b`是`new`出来的，其他的都是从缓存值获取的，通过==判定其引用是同一个，所以相等。在`a2`、`b2`、`c2`和`d2`中，由于其大小超出了默认缓存值，所以大家都是各自的新引用，也就各不相同了。

------

## 延伸

### 官方注释

 > Cache to support the object identity semantics of autoboxing for values between -128 and 127 (inclusive) as required by JLS. The cache is initialized on first usage. The size of the cache may be controlled by the -XX:AutoBoxCacheMax=<size> option. During VM initialization, java.lang.Integer.IntegerCache.high property may be set and saved in the private system properties in the sun.misc.VM class.

也就是说，我们可以通过`-XX:AutoBoxCacheMax=<size>`来设定缓存最大值，但是缓存的最小值已经被final掉了，固定是 __-128__，这个改变不了。

### 拓展问题

基于问题中的变量，我们进行以下方式的操作，结果又如何呢：

```java
    a += 99;
    b += 99;
    c += 99;
    d += 99;

    System.out.println(a == b);
    System.out.println(a == c);
    System.out.println(a == d);
```

答案是都相同，因为这时，所有的变量值都处于`Integer`缓存值区间，引用的都是缓存值。也就是说如果这四个变量加的是100，那么它们的值达到了`128`，就会由新的`Integer`对象来接管，使得这四个变量各不相等。
