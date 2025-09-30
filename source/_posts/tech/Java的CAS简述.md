---
title: Java的CAS简述
tags:
  - Java
published: true
abbrlink: 36778
date: 2023-01-15 10:13:07
category:
	- 技术
---
*以下内容基于Java1.8编写*

首先需要明白一点，CAS的全称是 __compare and switch__，也就是比较与交换，目的在于对 __set__ 做非阻塞原子操作，常用于多线程环境。

## atomic包下的CAS

`atomic`包下的类主要是为了对单一数据进行原子操作，比如`AtomicInteger`、`AtomicReference`、`AtomicStampedReference`等。

这些类中的`get`和`set`方法几乎都是简单的取值和赋值，并未做线程安全优化，只有像是`compareAndSet`这类的方法才是原子操作。

```java
public class AtomicInteger extends Number implements java.io.Serializable {
    /**
     * Atomically sets the value to the given updated value
     * if the current value {@code ==} the expected value.
     *
     * @param expect the expected value
     * @param update the new value
     * @return {@code true} if successful. False return indicates that
     * the actual value was not equal to the expected value.
     */
    public final boolean compareAndSet(int expect, int update) {
        return unsafe.compareAndSwapInt(this, valueOffset, expect, update);
    }
}
```

从源码中可以看出，这些方法主要还是调用的`unsafe`类的方法。在不知道unsafe是什么的情况下，只需要知道，`atomic`调用的这些方法都是原子操作就行了。

## Unsafe中的CAS

首先从名字上看就可以看出这个类是不安全的，主要原因在于它提供了很多内存有关的方法，这导致了JVM管理的内存机制可能会失效，并且会出现类似于 __C__ 中的指针问题。就像是反射一样，错误使用可能会导致很多不可预料的问题。

### Unsafe类的获取

`Unsafe`是一个单例模型，并不能直接`new`出来，而是通过`Unsafe.getUnsafe()`的方式获取。但如果你真的这样写了，你会发现程序运行到这里时会抛出异常：`java.lang.SecurityException: Unsafe`。

```java
public final class Unsafe {
    
    @CallerSensitive
    public static Unsafe getUnsafe() {
        Class var0 = Reflection.getCallerClass();
        if (!VM.isSystemDomainLoader(var0.getClassLoader())) {
            throw new SecurityException("Unsafe");
        } else {
            return theUnsafe;
        }
    }
}
```

从源码中可以看出，在调用`getUnsafe`方法时，会检测当前的调用类是否会通过`isSystemDomainLoader`方法校验。
也就是说，只有被引导类加载器加载的类才允许调用`Unsafe`方法。所以如果我们需要调用`Unsafe`类的话，一般用的都是反射方式：

```java
public class UnsafeTime {
    
    public static Unsafe getUnsafe() {
        Class<Unsafe> klass = Unsafe.class;
        Field field = klass.getDeclaredField("theUnsafe");
        field.setAccessible(true);
        Unsafe unsafe = (Unsafe) field.get(null);
    }
}
```

### Unsafe的CAS调用

`Unsafe`类不止 __CAS__ 方法，但篇幅有限，这里只介绍 __CAS__ 方法。

```java
public final class Unsafe {
    compareAndSwapInt(Object var1, long var2, int var4, int var5);
}
```

这里用int方法举例，参数列表为：

1. `var1` - 需要修改的值的对象
2. `var2` - 修改的目的值在内存中偏移量，一般可以通过`unsafe.objectFieldOffset`来获取，其参数是`Field`，也就是从类的存储结构中自动定位目的值偏移量。
3. `var4` - 期望值，也就是为了避免在调用时有其他线程同步修改，这里需要判断期望值是否与目的值相同。如果不同则表示此时数据已被修改，返回`false`。
4. `var5` - 目标值，也就是`set`过去的值。只有当此方法返回`true`时才会`set`成功。

举例：

```java
public class Test {
    
    public void setName() {
        Person person = new Person();
        person.setName("小明");
        System.out.println(person.getName());
        unsafe.compareAndSwapObject(person, unsafe.objectFieldOffset(Person.class.getDeclaredField("name")), "小明", "小红");
        System.out.println(person.getName());
    }
}
```

控制台打印结果如下：

```text
小明
小红
```

## ABA问题

ABA问题的出现主要是`compare`的时候，可能其他线程同步修改了数据后，数据和期望值相同，但此时的逻辑流程已经不是`compare`时的流程了。

__CAS__ 为了解决这类问题，提供了类似版本号的一个数值，在`compare`期望值时，会同时校验版本号是否相同。

## 其他

1. `ConcurrentHashMap`中的`putVal`方法其实就是用的 __CAS__ 和`synchronized`关键字实现的。
2. `ReentrantLock`的`NonfairSync`也是使用了 __CAS__ 的方式进行状态设定。