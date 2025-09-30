---
title: Thread.join
tags:
  - Java
  - Thread
published: true
abbrlink: 19774
date: 2022-03-04 10:08:32
category:
	- 技术
---
以前在学`Thread`的时候，就只知道`Runnable`是运行体，然后通过`start`方法来运行，也可以通过`isAlive`来检测是否在运行。  
这些基础的用法在大部分时候还是够用的，不过后面也逐渐接触到`ThreadPoolExecutor`来管理线程，也开始使用`join`等方法来进行线程同步。  
这里就简单记录一下我学习到的`join`方法。

## join()

基础的join方法，不带参数。  
效果是让当前线程在执行到`join`时，在目标线程结束前阻塞。只有目标线程结束后，当前线程才会进行继续执行。  
例如：

```java
Thread thread = new Thread(() -> {
    try {
        Thread.sleep(1000);
    } catch (Exception ignored) {
    }
    System.out.println("Hello World!");
});
thread.start();
thread.join();
System.out.println("OK!");
```

这里的`join`方法会让程序进行到此处时，等待`thread`线程结束，然后才会继续执行。  
所以控制台会输出以下结果：

```text
Hello World!
OK!
```

当我们把`thread.join();`去掉后，由于thread线程中，会等待一秒才会输出，所以结果会变成：

```text
OK!
Hello World!
```

并且若在此时，我们在`start`前加上`thread.setDaemon(true);`，则只会输出`OK!`。

## join(long)与join(long, int)

与join方法类似，这里的参数表示了一个持续时间，用于控制等待的最长时间。  
当目标线程执行的时间超过这个参数时，当前线程就不会再等待了，而是继续执行下面的代码。  

例如：

```java
Thread thread = new Thread(() -> {
    try {
        Thread.sleep(1000);
    } catch (Exception ignored) {
    }
    System.out.println("Hello World!");
});
thread.start();
thread.join(500);
System.out.println("OK!");
```

这里我们等待thread线程500毫秒，但是很明显，thread线程需要至少1000毫秒才会结束，所以获得到如下输出：

```text
OK!
Hello World!
```

## 总结

`join`方法一般适用于对多个线程进行数据同步，需要等待所有的线程都获取完数据后，才能进行下一步操作。