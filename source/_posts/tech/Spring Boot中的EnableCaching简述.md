---
title: Spring Boot中的EnableCaching简述
tags:
  - Java
  - SpringBoot
published: true
abbrlink: 7997
date: 2022-07-28 10:11:49
category:
	- 技术
---
**spring boot**中自带有数据缓存机制，主要通过其`org.springframework.cache`包下的各种类来实现。

## EnableCaching

`@EnableCaching`是启用缓存的注解，标注在任何一个可自动注入的类上即可开启。

## Cacheable

`@Cacheable`是一个标注与类与方法上的注解，用于表示此类或此方法需要使用缓存机制。当类与方法上都有时，采用 **就近原则**。

在`@Cacheable`注解中，有一些常用参数可以进行配置：

- `value`与`cacheNames` - 表示绑定的缓存名称。这里的缓存指的是单个的缓存存储器，并不是最终的键值对缓存对象。
- `key` - 表示缓存对象的 **key**，这个才是最终的缓存键值对的 **key**。这里的参数需要使用 **SpEL**表达式。
- `keyGenerator` - 表示用于生成此方法 **缓存key**的类。与`key`参数只能选择一个添加，否则会抛出`IllegalStateException`异常。
- `cacheManager` - 指定缓存管理器。这个后面再细说。
- `condition` - 缓存的条件。支持SpEL，当缓存条件满足时，才会进入缓存取值模式。
- `unless` - 排除的条件。支持SpEL，当排除的条件满足时，会直接调用方法取值。
- `sync` - 异步缓存模式。是否采用异步的方式，在方法取值时异步缓存。默认`false`，在缓存完成后才返回值。

一般情况下，可以这样使用：

```java
@RestController
@RequestMapping("cache")
@Cacheable(value = "cache", sync = true)
public class CacheController {

    @Cacheable(value = "hello", sync = true, keyGenerator = "myKeyGenerator")
    @GetMapping("hello")
    public String hello(String name) {
        System.out.println("name - " + name);
        return "hello " + name;
    }

    @GetMapping("hello2")
    public String hello2(@RequestParam(defaultValue = "1") Integer size, @RequestParam(defaultValue = "world") String name) {
        System.out.println("name - " + name);
        return "hello " + name;
    }
}
```

- 这里的`CacheController`被标记上了`@Cacheable(value = "cache", sync = true)`，表示其下的方法默认使用名为`cache`的缓存存取器，并采用异步的方式进行缓存处理。
- `hello`方法上同样添加了`@Cacheable(value = "hello", sync = true, keyGenerator = "myKeyGenerator")`，使得`hello`方法使用了独立的缓存设置，并通过`myKeyGenerator`的策略来生成 **缓存key**。

## CachePut

将方法返回值存入到缓存中，一般情况下是用在更新操作中，并于`Cacheable`与`CacheEvict`配合使用。

## CacheEvict

清除缓存值，一般用在删除或更新操作中，并于`Cacheable`与`CachePut`配合使用。

并且在CacheEvict注解中，多了两个参数：

- `allEntries` - 清除当前`value`下的所有缓存。
- `beforeInvocation` - 在方法**执行前**清除缓存。

示例代码示例如下：

```java
    @Cacheable(value = "c", key = "123")
    @GetMapping("hello")
    public String hello(String name) {
        System.out.println("name - " + name);
        return "hello " + name;
    }

    @GetMapping("/put")
    @CachePut(value = "c", key = "123")
    public String put() {
        return "hello put";
    }

    @GetMapping("/evict")
    @CacheEvict(value = "c", key = "123")
    public String evict() {
        return "hello put";
    }
```

上述代码中，访问`hello`接口时，会从**c缓存存取器**中取出**key**为`123`的缓存数值，没有则会调用方法并进行缓存。

访问`put`接口时，会将**c缓存存取器**中**key**为`123`的缓存值改为`hello put`，没有则进行缓存。

访问`evict`接口时，会将**c缓存存取器**中**key**为`123`的缓存值删除，此时访问`hello`接口会重新调用方法并进行缓存。

## CacheConfig

`@CacheConfig`作为类上的注解，目的是为了统一配置其下的方法缓存参数，并设定共享缓存名。

- `cacheNames` - 共享缓存名数组。设定后表示此类下的方法缓存会依次从这些缓存存取器中取值，如果有，则取用缓存值；若没有则调用方法取值，并缓存值到设定的**所有**缓存存取器中。

## CacheManager

缓存管理器接口，用来做缓存管理的类。一般我们需要自定义缓存策略时，就是从`CacheManager`来入手的。

直接上实例：

```java
@Component
public class MyCacheManager implements CacheManager, InitializingBean {

    private final Map<String, Cache> cacheMap;

    public MyCacheManager() {
        cacheMap = new HashMap<>();
    }

    @Override
    public Cache getCache(String name) {
        System.out.println("正在获取缓存 - " + name);
        return cacheMap.computeIfAbsent(name, MyCache::new);
    }

    @Override
    public Collection<String> getCacheNames() {
        return cacheMap.keySet();
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("say something!");
    }

}
```

`CacheManager`有两个方法需要被实现：

- `getCache(String)` - 获取缓存存取器。这里的`name`其实就对应了`@Cacheable`注解中的`value`与`cacheName`参数。
- `getCacheNames` - 获取类中所有缓存的名称集合。这主要是为了`Spring`内部的统一管理需要。

因为 **Spring**采用了默认替补策略，所以我们使用`@Component`或是通过`@Bean`自动注入后，默认的缓存管理器就会切换成我们自定义的。如果我们自定义了两个的话，可以通过`@Primary`来设定默认管理器。

## Cache

缓存存取器，用来管理缓存键值对的基本单元。

为了能对不同的缓存采用不同的存取策略，我们可以定制不同的`Cache`，并通过自定义的`CacheManager`的`getCache`方法返回对应的`Cache`。

举个例子：

```java
public final static class MyCache extends ConcurrentMapCache {

    public MyCache(String name) {
        super(name);
    }

    @Override
    public <T> T get(Object key, Class<T> type) {
        System.out.println("正在读取 - " + key);
        return super.get(key, type);
    }

    @Override
    public <T> T get(Object key, Callable<T> valueLoader) {
        System.out.println("正在读取 - " + key);
        return super.get(key, valueLoader);
    }

    @Override
    public ValueWrapper get(Object key) {
        System.out.println("正在读取 - " + key);
        return super.get(key);
    }
}
```

这里的`MyCache`集成了`ConcurrentMapCache`，并对每次缓存值的获取都进行了控制台输出。

## KeyGenerator

缓存key生成器，用于自定义规则**缓存key**的生成。

其接口的方法只有一个：

```java
public interface KeyGenerator {
    Object generate(Object target, Method method, Object... params);
}
```

一目了然，通过调用的目标对象、目标方法与方法入参进行**key**的生成。这里不做过多赘述。

不过需要注意的是，由于不同类可能有同名同参数的方法，这里建议加上`target.getClass().getName()`来作为标记，避免出现不希望的缓存映射。
