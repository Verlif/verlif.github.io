---
title: Callable的简单应用
tags:
  - Java
published: true
abbrlink: 24252
date: 2022-03-21 10:09:10
category:
	- 技术
---

`Callable`主要是用于后台获取数据，就像`Thread.join()`一样，可以当前线程等待后台线程执行返回。

`Callable`与`Runnable`相似，都是函数式接口，都只是描述了执行方法。不过`Callable`是通过`call()`方法执行，允许抛出异常，并且需要一个返回值。

`Callable`一般是和`FutureTask`结合使用。看源码不难得知，`FutureTask`实现了`RunnableFuture`接口，那么它就拥有`Runnable`与`Future`的特性。
所以我们可以通过`Thread`的方式来执行它，例如：

```java
Callable<String> callable = () -> {
    Thread.sleep(1000);
    System.out.println(Thread.currentThread().getName());
    System.out.println("wuhu");
    return "haha";
};
FutureTask<String> task = new FutureTask<>(callable);
EXECUTOR.execute(task);
System.out.println(Thread.currentThread().getName());
System.out.println(task.get());
```

这里所得的结果就是：

```text
main
Thread-0
wuhu
haha
```

这是因为在执行`FutureTask.get()`方法时，就像`Thread.join()`一样，需要等待`Callable`执行完毕并返回。

## 为什么不使用Runnable+Thread.join()

如果我们定义了变量，我们同样可以使用`Thread.join()`来同步线程的数据获取流程。但是`Runnable`是不能抛出异常的，也就是在数据获取的过程中出现错误是没办法直接反馈给获取方的。这就导致了通过`Runnable`方式来获取数据只能得到结果，错误结果只能在`Runnable`内部消化，这样可能会导致耦合度变高。
还有一点，由于`FutureTask`是`Future`接口的实现类，所以我们可以通过`FutureTask`来对这个异步任务进行取消或是确认完成的操作，这对一些自动化任务很有帮助。

并且`FutureTask`也为我们提供了`new FutureTask(Runnable runnable, V result)`的构造方法，可以兼容`Runnable`执行体。