---
title: Spring Boot的Scheduled
tags:
  - Java
  - SpringBoot
published: true
abbrlink: 30710
date: 2022-07-28 10:11:19
category:
	- 技术
---
**Spring Boot**的`Scheduled`用于做计划任务，例如周期任务、定时任务、延迟任务等。\n\n一般情况下，我们可以通过`@Scheduled`注解中的`cron`、`fixedDealy`、`fixedRate`、`initialDelay`这些属性来控制方法的调用策略。

## 基础参数

### cron

cron是一个时间表达式，使用了Spring自己的解析策略，表达式格式如下：

```text
{秒数} {分钟} {小时} {日期} {月份} {星期} {年份(可为空)}
```

#### 举个例子

- `@Schedule(cron = "0 0 2/3 * * ?")`表示了从2点开始，每3小时的进行一次触发，时间表为`2:00`、`5:00`、`8:00`、`11:00`、`14:00`、`17:00`、`20:00`、`23:00`这些时间。注意，这里的触发时间会在每日刷新，第二日重新开始。也就是说此表达式其实已经固定了执行时刻表，就是上述的这些时间。
- `@Schedule(cron = "* 5-15 * * * ?")`表示了在每小时的5-15分钟时进行触发，像是`7:05`、`7:06`、`7:07`等的这些时间。

#### 细节表述

- {秒数}{分钟} ==> 允许值范围: 0~59 ,不允许为空值，若值不合法，调度器将抛出SchedulerException异
- {小时} ==> 允许值范围: 0~23 ,不允许为空值，若值不合法，调度器将抛出SchedulerException异常,占位符和秒数一样
- {日期} ==> 允许值范围: 1~31 ,不允许为空值，若值不合法，调度器将抛出SchedulerException异常
- {星期} ==> 允许值范围: 1~7 (SUN-SAT),1代表星期天(一星期的第一天)，以此类推，7代表星期六(一星期的最后一天)，不允许为空值，若值不合法，调度器将抛出SchedulerException异常
- {年份} ==> 允许值范围: 1970~2099 ,允许为空，若值不合法，调度器将抛出SchedulerException异常
- “*” 代表每隔1秒钟触发；
- “,” 代表在指定的秒数触发，比如”0,15,45”代表0秒、15秒和45秒时触发任务
- “-“代表在指定的范围内触发，比如”25-45”代表从25秒开始触发到45秒结束触发，每隔1秒触发1次
- “/”代表触发步进(step)，”/”前面的值代表初始值(““等同”0”)，后面的值代表偏移量，比如”0/20”或者”/20”代表从0秒钟开始，每隔20秒钟触发1次，即0秒触发1次，20秒触发1次，40秒触发1次；”5/20”代表5秒触发1次，25秒触发1次，45秒触发1次；”10-45/20”代表在[10,45]内步进20秒命中的时间点触发，即10秒触发1次，30秒触发1次

注意：除了{日期}和{星期}可以使用”?”来实现互斥，表达无意义的信息之外，其他占位符都要具有具体的时间含义，且依赖关系为：年->月->日期(星期)->小时->分钟->秒数

数据来源：[博客园](https://www.cnblogs.com/qianjinyan/p/10415140.html)

### fixedDelay

顺延任务，在此任务 **完成后**，延时一段时间后再次执行。

#### 举个例子

- `@Scheduled(fixedDelay = 1000L)`，每1秒钟执行一次，其中可以通过`timeUnit`来更改时间单位，默认为毫秒。

### fixedRate

周期任务，在此任务 **开始时**，延时一段时间后再次执行。可以看到，`fixedRate`与`fixedDelay`的差别只在计时上。

#### 举个例子

- `@Scheduled(fixedDelay = 1000L)`，每1秒钟执行一次，其中可以通过`timeUnit`来更改时间单位，默认为毫秒。

### initialDelay

初始化延迟，也就是在SpringBoot启动时，需要延迟多久才进行第一次的执行。这个参数同样会延后`fixedDealy`、`fixedRate`的判定。

`initialDelay`是无法与`cron`共同存在的，否则会抛出`IllegalStateException`异常。

## 多线程执行

**Spring Boot**中的计划任务是异步执行的，但默认的执行线程池大小只有 **1**，所以所有的计划任务都是排队执行，这就导致了任何一个任务的执行时长都可能影响到其他任务的执行。

### 使用自定义的任务执行器

通过自定义任务执行器来改变任务执行方式。

例如：

```java
@Configuration
public class ScheduleExecutor {

    @Bean
    public TaskScheduler taskScheduler() {
        ThreadPoolTaskScheduler taskScheduler = new ThreadPoolTaskScheduler();
        taskScheduler.setPoolSize(5);
        return taskScheduler;
    }

}
```

尽管通过自定义的执行器可以使每个任务都使用自己的线程，但每个任务与自身的执行时长依旧相关。

例如：

```java
    @Scheduled(fixedRate = 1000L)
    public void taskTwo() throws InterruptedException {
        LOGGER.info("taskTwo");
        Thread.sleep(2500);
    }
```

上述任务是每1秒执行一次，但任务耗时2.5秒。在使用自定义任务执行器时，输出结果如下：

```text
2022-07-26 10:35:35.158  INFO 18644 --- [taskScheduler-2] i.v.study.spring.schedule.ScheduleTask   : taskTwo
2022-07-26 10:35:37.660  INFO 18644 --- [taskScheduler-4] i.v.study.spring.schedule.ScheduleTask   : taskTwo
2022-07-26 10:35:40.160  INFO 18644 --- [taskScheduler-5] i.v.study.spring.schedule.ScheduleTask   : taskTwo
```

可以看到尽管每次执行任务的线程不同，但执行间隔依旧是2.5秒，所以对于每个任务本身而言，自定义执行器并不会消除执行时长的影响。

### 通过Asyn注解

与`@EnableScheduling`类似，只需要在任何一个可注入类上标记`@EnableAsync`，即可启用异步任务。

随后在需要使用异步的方法上添加`@Aync`注解即可。需要注意的是，`@Aync`标记的方法必须是可重写的，通常情况下就是`public`。\n\n例如：

```java
    @Async
    @Scheduled(fixedRate = 1000L)
    public void taskTwo() throws InterruptedException {
        LOGGER.info("taskTwo");
        Thread.sleep(2500);
    }
```

这里我们加上`@Aync`注解，执行结果如下：

```text
2022-07-26 10:46:51.117  INFO 11156 --- [         task-3] i.v.study.spring.schedule.ScheduleTask   : taskTwo
2022-07-26 10:46:52.117  INFO 11156 --- [         task-4] i.v.study.spring.schedule.ScheduleTask   : taskTwo
2022-07-26 10:46:53.116  INFO 11156 --- [         task-5] i.v.study.spring.schedule.ScheduleTask   : taskTwo
2022-07-26 10:46:54.125  INFO 11156 --- [         task-6] i.v.study.spring.schedule.ScheduleTask   : taskTwo
```

这里就是每一秒都在执行，并且每个任务就是独立的，并不会受到上一个任务的执行时长影响。但这同样存在一个问题，当我们将`fixedRate`改为`fixedDelay`时，输出结果同样是每秒执行一次，这就与我们的预期不符合了。

## 其他

- `fixedDealy`、`fixedRate`、`initialDelay`这三个参数都可以使用`timeUnit`来更改时间单位，默认为毫秒。
- `fixedDealy`、`fixedRate`、`initialDelay`这三个参数都有其对应的String参数，可以直接书写数字，例如`@Scheduled(fixedRateString = "1000")`。不过此参数一般都用于解析**SpEL**表达式，例如读取配置中的数字：`@Scheduled(fixedDelayString = "${task.two}")`
- 在对任务进行多线程处理时，需要按照自身需要来选择使用自定义任务执行器或是`@Aync`注解。
