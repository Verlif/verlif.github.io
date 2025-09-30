---
title: 链式调用与Builder
tags:
  - Java
category: 技术
abbrlink: 32703
date: 2024-05-15 14:57:21
---
建造者（Builder）模式又是为了方便构造参数，采用链式调用可以更方便地填充对象参数。
<!-- more -->

首先我们先了解一下链式调用。

> 链式调用的本质是使调用方法返回下一个需要操作的对象，从而将数个方法通过一条语句的方式执行完毕。

我们比较常接触的链式调用就有`StringBuilder`，就像这样：

```java
StringBuilder stb = new StringBuilder()
        .append("123")
        .append("321")
        .append("1234567");
```

和`StringBuilder`一样，很多的`Builder`也是采用了链式调用的方式让开发者使用。那么为什么建造者模式适合用链式调用风格呢？

我们先来看看什么是建造者模式。

## 建造者模式

建造者模式是一种设计模式，它描述了一个对象的创建过程。与直接`new Object()`不同，我们不直接将这个对象`new`出来，而是先创建一个它的建造者`Builder`，再通过`Builder`来创建这个对象。

例如**OkHttp3**中创建一个客户端对象：

```java
OkHttpClient client = new OkHttpClient.Builder()
        .readTimeout(30, TimeUnit.SECONDS)
        .addInterceptor(new CacheInterceptor(new Cache(new File("cache"), 20000)))
        .build();
```

你可能就要问了，为什么不直接`new OkHttpClient()`呢，还要绕一圈用另外的对象来创建我们需要的对象？

首先，每个对象的建造者的目的都可能不一样，原因可能有这些：

- 参数过多，使用`set`方式代码太多不够简洁
- 存在`set`的内部逻辑，为了保持目标类`set`的纯净性，使用了建造者代理的方式
- 屏蔽目标类的参数限制，比如过多的`final`参数使得直接构造目标类不方便
- 延迟构建，降低不必要的性能消耗

所以建造者模式实际上就是辅助我们构建对象的工具，而链式调用就可以很直观地看出我们的建造过程，方便书写和理解。

### 举例

比如我们的建造者可以是这样的：

```java
public class User {

    private String name;

    private String id;

    /* Getter&Setter */

    public static final class Builder {

        private final User user;
        
        public Builder() {
            this.user = new User();
        }
        
        public Builder name(String name) {
            user.setName(name);
            return this;
        }
        
        public Builder id(String id) {
            user.setId(id);
            return this;
        }

        public User build() {
            return user;
        }
    }
}
```

这里我们使用的就是内部静态类的方式，将每一个参数都进行了代理，这样我们就可以用下面这种方式来生成`User`对象了：

```java
User user = new User.Builder().id("123").name("小明").build();
```

此时你会发现另一个问题，我们写了这么长一串，为什么不直接用更简洁的构造参数`new User("123", "小明")`呢？

因为正常情况下，在参数较少时我们并不会用建造者模式，一般只有在构造对象复杂的情况下才会用到建造者。

## 总结

建造者模式只是为了方便创建对象，实际上对于简单的POJO对象，我们甚至可以直接用建造者风格的set方法：

```java
public class User {

    private String name;

    private String id;

    public String getName() {
        return name;
    }

    public User setName(String name) {
        this.name = name;
        return this;
    }

    public String getId() {
        return id;
    }

    public User setId(String id) {
        this.id = id;
        return this;
    }
}
```

设计模式只是经过时间和代码量证明了的编程经验，但并不是真理。随着时代发展，设计模式也会不断地演化更替，只有适合自己（或是需求）的才是最好的。