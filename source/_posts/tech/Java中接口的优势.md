---
title: Java中接口的优势
tags:
  - Java
published: true
abbrlink: 50230
date: 2024-01-24 14:22:23
category:
	- 技术
---
**接口**是一个**抽象**概念的实例化，它表示了一个类所能实现的方法与属性值。但在**Java**中，`interface`作为一个类型，就额外拥有了一些特点。

*以下内容基于Java8书写*

## 定义一个接口

接口被`interface`关键字定义且无法被`final`所描述，自带有`abstract`修饰符。在不同的位置下，接口修饰符的限制有所不同：

- 同名类文件中（独立接口），只允许`public`权限修饰符，不接受`static`关键字。
- `class`内部类，接受`public`、`protect`、`private`权限修饰符，默认且只允许`static`修饰符。
- `interface`内部类，自带且只允许`public static abstract`修饰符。

举例：

```java
public interface AInterface {

    String hello();

    interface BInterface {
    }
}
```

### 定义接口方法

接口方法只接受`public abstract`修饰符，而`default`修饰符在接口方法中只表示默认实现，所处位置在权限修饰符后与`abstract`同级，因此不提供权限修饰作用。被`default`修饰的接口方法不再强制实现类实现。↓

```java
public interface AInterface {

    String helloWorld = "Hello world!";

    default String hello() {
        return helloWorld;
    }

    class BClass implements AInterface {
    }
}
```

这里的`BClass`在实现`AInterface`时就不需要实现`hello()`方法。

### 定义接口属性

接口属性只能被`public static final`修饰（所以一般可以不写修饰符），因此存在于接口中的属性必定可以使用`接口名.属性名`方式进行访问。

例如`java.lang.reflect.Member`接口就定义了`PUBLIC`和`DECLARED`属性。↓

![Member接口](/images/1706082124030.png)

### 注意

**Java8**中不支持本地接口（方法中定义接口）
**Java7及以前版本**的接口方法无法在接口中实现（对接口方法进行**default**实现）

## 接口的使用

接口可以被实现与继承，并且接口是**Java**中唯一支持**多继承**的类型。

### 接口的继承

例如以下接口定义中，`AInterface`继承于`Map`、`Cloneable`和`Serializable`接口，这里需要说明一点，**interface**只能继承**interface**，**class**只能实现**interface**，**interface**无法继承**class**。↓

```java
public interface AInterface<K, V> extends Map<K, V>, Cloneable, Serializable {

    String hello();

}
```

这表示，`AInterface`接口也继承了`Map`、`Cloneable`和`Serializable`接口的属性与方法，也就是说可以像使用`Map`、`Cloneable`和`Serializable`接口一样去使用`AInterface`接口。

### 接口的实现

比如经典的`HashMap`类就是实现了`Map`、`Cloneable`和`Serializable`接口。↓

![HashMap](/images/1706080927570.png)

因此，`HashMap`就需要实现它继承的接口的所有**非default修饰方法**。当然，实现类也可以重写被`default`修饰的方法。

## 作用

### 定义规范

接口的主要作用就是定义一个规范，就像是协议一样，让协议实现方通过接口实现的方式进行统一开发，而使用方只需要关注协议内容即可。双方都面向接口开发可以大大降低耦合度，极大地提高开发维护效率。

举例：

假设我们有一个县长接口：↓

```java
public interface Xianzhang {

    Double power();

    BigInteger wealth();
}
```

县长接口为我们提供了`power`和`wealth`两个方法。这时我们用一个类`Tang`来实现县长接口：↓

```java
public class Tang implements Xianzhang {
    @Override
    public Double power() {
        return 1.0;
    }

    @Override
    public BigInteger wealth() {
        return new BigInteger("12");
    }
}
```

此时，`Tang`就获得了县长类型，我们就可以这样来使用它：↓

```java
public static void main(String[] args) throws Exception {
    Xianzhang tang = new Tang();
    handle(tang);
}

private static void handle(Xianzhang xianzhang) {
    Double power = xianzhang.power();
    BigInteger wealth = xianzhang.wealth();
}
```

这里我们就获得了`Tang`的`power`和`wealth`。那么我们就有一个疑问了，为什么要使用`Xianzhang tang = new Tang();`为不是`Tang tang = new Tang();`呢，明明效果都一样啊？因为我们可能还有一个县长的实现类`Wang`：↓

```java
public class Wang implements Xianzhang {
    @Override
    public Double power() {
        return 10.0;
    }

    @Override
    public BigInteger wealth() {
        return new BigInteger("22");
    }
}
```

当我们没有`Tang`的时候，只需要把`new Tang()`换成`new Wang()`就好了，`handle()`方法也不需要改变，因为我们定义是以`Xianzhang`来定义的，我们就只需要关注`Xianzhang`接口为我们提供了什么方法就可以了，谁来实现都无所谓。

### 定义常量

很多时候我们会用到一些配置量，例如**每周的天数**、**光速**、**字符串分隔符**等等。

我们需要一个专有的类来管理这些全局配置，那么接口就非常适合用来存放这些**静态常量**：↓

```java
// 这里只做演示，常量在实际编写时需要归类，使用不同的接口管理
public interface Global {

    int DAY_OF_WEEK = 7;
    BigDecimal SPEED_OF_LIGHT = new BigDecimal("299792458");
    char SPLIT = ' ';

}
```

当然，并不是说静态常量都要放在接口中，只是因为放置于接口中便于维护与使用。

### 定义类型

定义类型的目的一般用在类筛选上，某些方法只对特殊的类做处理就可以使用**空接口**，例如`Serializable`接口：↓

![Serializable](/images/1706085160280.png)

当我们实现去定义类型的空接口时，就表示我们知道我们在做什么，类似于签署了一个协议。

比如我开发了一个功能，传入一个对象就可以将这个对象的所有属性值清空。但是为了避免使用者误用这个方法，我可以定义一个`Clearable`接口，并将`clear`方法入参锁定成`Clearable`。这样，只有**继承了`Clearable`接口的类的实例**才允许被属性清空：

```java
// 属性清空方法
public void clear(Clearable clearable) {
    // TODO: 清空clearable对象属性
}

// Clearable类型接口
public interface Clearable {
}
```

## 完毕