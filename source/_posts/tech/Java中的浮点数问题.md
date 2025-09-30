---
title: Java中的浮点数问题
tags:
  - Java
published: true
abbrlink: 29122
date: 2022-02-27 10:07:23
category:
	- 技术
---
一般情况下，我们会使用`double`或是`float`来作为小数运算对象，不过因为浮点数的存储问题，导致了浮点数的精度丢失。
所以一般情况下，在需要精确计算时，我们会用到`BigDecimal`。

参考资料: [知乎专栏](https://zhuanlan.zhihu.com/p/164568001)

## 案例

以下是浮点数精度丢失的演示:

```java
System.out.println(0.06 + 0.01);
System.out.println(1.0 - 0.42);
System.out.println(4.015 * 100);
System.out.println(303.1 / 1000); 
```

预期的值应该是:

```text
0.07
0.58
401.5
0.3031
```

但运行下来得到的结果却是:

```text
0.06999999999999999
0.5800000000000001
401.49999999999994
0.30310000000000004
```

这就是是因为浮点数末位精度问题，如果对以上的结果进行四舍五入的话就可以得到正确的值。
不过在使用中，我们还是更偏向于使用`BigDecimal`。

## BigDecimal

`BigDecimal`我一般叫它`大数`，从命名上就可以看出这个对象就是用来做大数字操作的。

使用方法可以是`new BigDecimal()`，也可以是`BigDecimal.valueOf()`。那么如果我们用下面的方式来进行浮点数运算是不是就没问题了呢？

```java
double d1 = 0.06;
double d2 = 0.01;
BigDecimal b1 = new BigDecimal(d1);
BigDecimal b2 = new BigDecimal(d2);

System.out.println(b1.add(b2).doubleValue());
```

得到的结果是`0.06999999999999999`，似乎和之前的错误结果是一样的。

这是因为`BigDecimal`在接收浮点数时，同样会丢失精度。运行以下代码，可以看出问题：

```java
System.out.println(new BigDecimal(0.06));
System.out.println(new BigDecimal(0.01));
```

得到的结果是：

```text
0.059999999999999997779553950749686919152736663818359375
0.01000000000000000020816681711721685132943093776702880859375
```

---

事实上，如果希望能精确地计算浮点数，需要使用`String`字符串作转换，由`BigDecimal`来生成浮点数。

```java
BigDecimal b1 = new BigDecimal("0.06");
BigDecimal b2 = new BigDecimal("0.01");

System.out.println(b1.doubleValue());
System.out.println(b2.doubleValue());
System.out.println(b1.add(b2).doubleValue());
```

这样才可以得到正确的结果：

```text
0.06
0.01
0.07
```

## 总结

在一般的计算下，例如计算时间、里程等不需要非常精确的数值时，可以使用浮点数运算，并通过四舍五入达到预期结果。

在需要精确计算的情况下，例如金额、误差等情形，就可以使用`BigDecimal`来运算，并且通过`Double.toString()`方法将目标浮点数转化成`String`字符串来达到预期。
