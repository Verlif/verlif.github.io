---
title: Java断言（assert）
tags:
  - 教程
  - JavaDoc
published: true
cover: /images/fhQTsN9oi.png
banner: /images/fhQTsN9oi.png
abbrlink: 61278
date: 2024-01-23 14:34:45
category:
	- 技术
---
`assert`是**Java**中用于做校验的一个**关键字**，类似于`if`，但`assert`并不具备分支逻辑，而是当断言表达式**为真时继续**，**为假时抛出错误^[java.lang.AssertionError]**。

使用举例：

```java
assert 1 > 2 : "1怎么会大于2";
```

打印结果为：

```text
Exception in thread "main" java.lang.AssertionError: 1怎么会大于2
	at study.verlif.test.Main.main(Main.java:6)
```

## 开启断言

当你准备测试，在程序中写入`assert 1 > 2 : "1怎么会大于2";`并运行时，你会发现什么都没有发生，这是因为Java断言是**关闭**的，需要通过JVM参数开启（`-enableassertions`或`-ea`）。

例如：`java -jar -ea assertTest.jar`

或是通过**IDEA**的运行配置进行测试：

![运行配置](/images/1705992812869.png)

## 书写断言

断言格式为`assert [断言表达式] : [失败描述]`，也可以`assert [断言表达式]`不写描述^[有些包提供了静态方法方便开发者以方法的方式进行书写，例如`JUint`、`Springframework`，其中的实现逻辑也不相同]。

其中断言表达式只能是一个返回`true`或是`false`的语句，包括**判断语句**、**方法**、**boolean属性变量**。

```java
boolean a = true;
// 断言1
assert a : "a怎么能是false呢？";
// 断言2
assert isNormal();
// 断言3
assert 1 > 2 : "1怎么会大于2！";
```

对于以上代码，其中**断言1为真**，不产生动作；**断言2**按照`isNormal()`运行结果来判断是否抛出错误；当**断言2为真时断言3会直接抛出`AssertionError`错误**。

## `AssertionError`错误

`AssertionError`继承于`Error`，而`Error`继承于`Throwable`，这三者都属于`java.lang`包下。

![AssertionError继承关系](/images/1705993624030.png)

## 使用场景

断言使用场景非常局限，因为其抛出错误的机制导致了判断逻辑的重要性，基本上不会出现在正式环境下，但是**测试环境**倒是很常用。

一般使用**JUnit**的时候，我们需要判断我们编写的功能或代码片段是否按期运行就会用到判断逻辑。单元测试下的判断逻辑一般就只会用来判断结果是否符合预期，不用做分支处理，此时使用`assert`就非常合适，而不是写一堆`if (!condition) throw new RuntimeException("结果错误");`