---
title: Callable与Future
tags:
  - Java
  - 多线程
abbrlink: 22258
date: 2024-12-24 10:04:06
---

<!-- more -->

## Callable

两年前我写过一篇[Callable的简单应用](../../2022/24252)，如今来看以前写的东西和没写没什么区别。

所以今天我详细记录一下我对于Callable的理解与使用。

### 设计逻辑

Callable与Runnable相似，都是单方法的接口，但Callable存在一个返回值，由类上的泛型定义。

```java
@FunctionalInterface
public interface Callable<V> {
    /**
     * Computes a result, or throws an exception if unable to do so.
     *
     * @return computed result
     * @throws Exception if unable to compute a result
     */
    V call() throws Exception;
}
```

这是因为`Callable`除了做运行逻辑的封装，还负责对外提供运行结果，结果类型就需要定义在类上。

举例，我现在在做饭，但正好快递到了需要我去取。此时我有以下的方式（包括但不限于）可以完成这两件事：

- 做完饭后再去取，我一个人就能完成（消耗资源少），但最终完成时间太久（耗时）
- 让别人（进程中的资源）帮忙去取，这样就不会耽误我做饭了（并行）

在不着急的情况下，我可以使用第一种方式，因为我对于这两件事情完全**可控**。

如果我使用了第二种方式，那么别人在帮我取完快递后，我还需要从别人那里拿到我的快递（执行结果）。

那么此时，我们就涉及到了多线程的结果处理方式。

### 线程

在程序运行中经常会遇到多个逻辑需要同时处理的情况，不过很多时候线性执行的效率比调度线程更快，因此我们大部分时候都是顺序编写。
但如果我们遇到需要同步计算以降低时间消耗的场景时，就需要设计并行逻辑了。

在操作系统中，我们用到的并行调度几乎都是由线程完成的。它作为系统调度的最小单位，可以并行运算以减少操作队列长度来节约时间。

转换到Java语言中，我们有这样的描述：

> `Runnable`是我们需要执行的原子操作，例如**做饭**是一个`Runnable`，**取快递**也是一个`Runnable`。
> `Thread`是我们直接执行**Runnable**的操作器，它同样实现了**Runnable**接口，因此它也可以看成一个原子操作。
> `Executor`是我们执行**Runnable**的执行器，与**Thread**不同的是，`Executor`并不直接绑定**Runnable**，而是通过`execute`的方式去执行；但**Thread**只能绑定一个**Runnable**，这表示如果你有两个**Runnable**，你就需要使用两个**Thread**。

我们大部分使用的并行相关的代码都在`java.util.concurrent`包下。有趣的是，`Thread`是在`java.lang`包下，因此我们不需要引入包即可使用`Thread`。

### Callable

既然`Runnable`是原子操作了，那么`Callable`是干什么的呢？

我们注意到，对于做饭与取快递来说，我们实际上是需要获取这两个行为的结果，过程（实现逻辑）其实并不是很重要。

但是`Runnable`只关注了行为，无法观测结果，因此如果要使用`Runnable`来管理结果，开发者就需要将行为结果的处理同样放在原子操作中。

这种方式对于多个关联任务来说并不友好，因此我们可以使用`Callable`来将结果返回出，交由任务提交者来处理，我们就可以更关注过程。

### 使用

由于`Callable`存在返回值，因此我们无法使用`Thread`与`Executor.execute`，而是使用`java.util.concurrent.Future`。

用法举例：

```java
public static void main(String[] args) throws Exception {
    // 定义Callable
    Callable<String> callable = () -> {
        // 模拟耗时
        Thread.sleep(1000);
        // 返回操作结果
        return "OK";
    };
    // 封装Callable到FutureTask任务实例
    FutureTask<String> future = new FutureTask<>(callable);
    // 启动新线程执行FutureTask任务实例
    new Thread(future).start();
    // 获取Callable结果
    System.out.println(future.get());
}
```

这里有两个地方值得注意：

#### 为什么需要FutureTask

实际上，与`Callable`配套使用的通常是`Future`，但`Future`也是接口，它只是定义了一套取值规范，因此我们需要结合实际情况来使用其具体实现类。

对于并行任务来说，我们常用`Runnable`处理，因此在`java.util.concurrent`包中存在`RunnableFuture`接口来结合`Callable`，而`RunnableFuture`的常用实现即是`FutureTask`。

#### 为什么future.get()像同步方法

因为future.get()实际是阻塞方法，需要等任务执行完毕后才进行返回。在实际使用中我们通常不会直接这样使用，而是像这样：

```java
public static void main(String[] args) throws Exception {
    Callable<String> callable = () -> {
        System.out.println("1");
        Thread.sleep(1000);
        System.out.println("2");
        return "OK";
    };
    // 封装Callable到FutureTask任务实例
    FutureTask<String> future = new FutureTask<>(callable);
    // 启动新线程执行FutureTask任务实例
    new Thread(future).start();
    // TODO: 其他耗时任务
    doSomething();
    // TODO: 处理结果
    String result = future.get();
    System.out.println(result);
}
```

也就是说，我们先将任务封装到新线程执行，随后开始我们的主任务。当主任务完结或是需要异步任务的结果时，我们再从future中取值。

### 其他使用方式

除了直接使用`FutureTask`，我们还有两种方式可以处理`Callable`：

```java
future.get(200, TimeUnit.MILLISECONDS)
```

通过增加计时等待的方式，当超时后`get`方法会抛出`TimeoutException`异常，此时开发者可以进行超时后操作，例如`future.cancel(true)`

另一种方式是提交给执行器去执行：

```java
public static void main(String[] args) throws Exception {
    Callable<String> callable = () -> {
        System.out.println("1");
        Thread.sleep(1000);
        System.out.println("2");
        return "OK";
    };
    ExecutorService executorService = Executors.newFixedThreadPool(1);
    Future<String> future = executorService.submit(callable);
    executorService.shutdown();
    System.out.println(future.get());
}
```

### Thread.join

从`Callable`中我们可以看出，其基本的设计逻辑是，将额外的任务先交给执行器去执行，并保存返回的`Future`。随后在需要处理结果的时候从`Future`中获取结果。

这样的执行逻辑与`Thread.join`非常像，但是`Future`允许获取执行结果的这一特点让它在并发场景下的使用频率大大增加。

## FutureTask

尽管在大多数场景下与Callable配套使用，但FutureTask仍然支持Runnable，例如这样：

```java
FutureTask<String> future = new FutureTask<>(new Runnable() {
    @Override
    public void run() {
        System.out.println("3");
    }
}, "OK");
```

不难看出，这样编写的结果就是，任务会在执行完`Runnable`的`run`方法后，返回**OK**。

### 取消任务

例如我们在某些情况下，例如任务超时，需要将任务取消掉，我们可以通过`Future`提供的`cancel`方法：

```java
public static void main(String[] args) throws Exception {
    Callable<String> callable = () -> {
        System.out.println("1");
        Thread.sleep(1000);
        System.out.println("2");
        return "OK";
    };
    // 封装Callable到FutureTask任务实例
    FutureTask<String> future = new FutureTask<>(callable);
    // 启动新线程执行FutureTask任务实例
    new Thread(future).start();
    try {
        String result = future.get(200, TimeUnit.MILLISECONDS);
        // TODO: 处理结果
        System.out.println(result);
    } catch (TimeoutException e) {
        future.cancel(true);
        // 处理超时
        System.out.println("任务超时");
    }
}
```

我们在`get`时设置了**200**毫秒的超时等待，但实际的任务会执行**1000**毫秒，那么在调用`get`方法时的**200**毫秒后，方法就会抛出`TimeoutException`异常，我们就可以在异常捕获后进行超时处理，例如回滚业务。