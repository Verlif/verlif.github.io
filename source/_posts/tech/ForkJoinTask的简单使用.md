---
title: ForkJoinTask的简单使用
tags:
  - Java
  - 多线程
abbrlink: 13608
date: 2024-12-25 10:14:00
---

<!-- more -->

## 简述

### 场景

合并运算的场景，例如批量数据组合运算。

例如在合并两个来源的数据时，我们往往需要通过两个方法来分别获取数据。当数据量或等待时间过长时，我们可以将这两个方法并行执行来缩短执行时间。

### 通常使用

1. 通过`fork()`方法将任务交给任务池异步执行
2. 使用`join()`方法阻塞式获取执行结果

举例：

```java
public static void main(String[] args) throws Exception {
    ForkJoinTask<Integer> task = new RecursiveTask<Integer>() {
        @Override
        protected Integer compute() {
            // TODO: 模拟计算耗时
            try {
                Thread.sleep(1200);
            } catch (InterruptedException ignored) {
            }
            System.out.println("计算完成");
            return 123;
        }
    };
    task.fork();
    // TODO: 模拟耗时任务
    Thread.sleep(1000);
    System.out.println("主线程完成");
    // 处理结果
    Integer result = task.join();
    System.out.println("计算结果：" + result);
}
```

## 方法简述

### fork

将任务推送到任务队列中异步执行。

由于`ForkJoinTask`的状态机制，导致了在任务执行途中（`isDone()`方法返回**false**时）再次调用`fork`时，会再次推送任务并异步执行。

但由于`fork`的返回值是自身，因此在`fork`后的`join`中，只会返回最近的一次缓存结果。

### join

等待进行中的任务并返回运算结果。

当任务被多次执行时，则会等待最后一次任务的执行返回。

```java
public static void main(String[] args) throws Exception {
    ForkJoinTask<Integer> task = new RecursiveTask<Integer>() {
        @Override
        protected Integer compute() {
            // TODO: 模拟计算耗时
            try {
                Thread.sleep(1200);
            } catch (InterruptedException ignored) {
            }
            int i = new Random().nextInt(100);
            System.out.println("计算完成 - " + i);
            return i;
        }
    };
    task.fork();
    Thread.sleep(200);
    task.fork();
    Thread.sleep(200);
    task.fork();
    Integer join = task.join();
    System.out.println(join);
}
```

这里的返回结果就是最后一次`fork`中计算的结果。

### invoke

**invoke**方法实质上是让任务尝试执行，阻塞并返回结果。

有一点值得注意，在调用**invoke**方法时，若任务处于运行中时，则任务会被再次执行。若任务已经结束，则直接返回结果。

示例如下：

```java
public static void main(String[] args) throws Exception {
    ForkJoinTask<Integer> task = new RecursiveTask<Integer>() {
        @Override
        protected Integer compute() {
            // TODO: 模拟计算耗时
            try {
                Thread.sleep(1200);
            } catch (InterruptedException ignored) {
            }
            int i = new Random().nextInt(100);
            System.out.println("计算完成 - " + i);
            return i;
        }
    };
    task.fork();
    // TODO: 模拟耗时任务
    Thread.sleep(1000);
    System.out.println("主线程完成");
    // 此时任务还未完成，调用invoke方法时会导致任务再次执行
    task.invoke();
    System.out.println("计算结果：" + task.getRawResult());
    // 此时任务已经完成，调用invoke方法则会直接返回缓存结果
    task.invoke();
    System.out.println("计算结果：" + task.getRawResult());
}
```

此时的结果输出如下：

```text
主线程完成
计算完成 - 92
计算完成 - 21
计算结果：21
计算结果：21
```

### getRawResult

直接返回任务的缓存结果。若无结果则返回`null`。