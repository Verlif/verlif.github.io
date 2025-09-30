---
title: Java的参数传递
tags:
  - Java
published: true
abbrlink: 65372
date: 2022-06-15 10:03:15
category:
	- 技术
---
众所周知，Java的参数采用的是 __引用传递__ 的方式。我以前总是会误以为方法参数采用的是 *值传递*，导致我错误判断的原因如下：

```java
public void change(Person person) {
    person.setName("Verlif");    
}
```

就像上面的代码一样，因为在方法中改变`person`的`name`，会对传入的`person`对象造成改变，我就以为这是值传递，这是理解上的问题。

当然，这并不是我在这里要说的。我要说的是我犯过的另一个错误。

## 演示

例如我们现在有一个接口：

```java
public interface Hello {
    String hello();
}
```

一个配置类：

```java
public class Config() {

    private Hello hello;

    public void setHello(Hello hello) {
        this.hello = hello;
    }

    public Hello getHello() {
        return hello;
    }
}
```

现在我已经创建了一个Hello的实现类：

```java
public class HelloImpl implements Hello {
    public String hello() {
        return "hello";
    }
}
```

假设我希望能对此接口进行一个初始化的处理，但又不想或不能创建新的类，那我可能会这样写：

```java
public class Config() {

    private Hello hello;
    
    public void init() {
        if (hello != null) {
            Hello nh = new Hello() {
                @Override
                public String hello() {
                    return "I said " + hello.hello();
                }
            };
            hello = nh;
        }
    }

    public void setHello(Hello hello) {
        this.hello = hello;
    }

    public Hello getHello() {
        return hello;
    }
}
```

然后我们进行测试：

```java
@Test
public void test() {
    Config config = new Config();
    config.setHello(new HelloImpl());
    config.init();
    System.out.println(config.getHello().hello());
}
```

理想的情况下，应该输出结果：`I said hello`，但是这些代码运行下来会出现栈溢出。

## 原因

原因就在这个地方：

```java
Hello nh = new Hello() {
    @Override
    public String hello() {
        return "I said " + hello.hello();
    }
};
hello = nh;
```

这里看着像是创建一个新的 __Hello实现类__ ，其方法表示为`"I said " + 原有的Hello对象的输出`，然后将 __hello引用__ 指向新创建的对象。

重点就在`hello.hello()`上。因为这里是进行的引用传递，所以当这一行代码运行后，`hello.hello()`中的 __hello对象__ 其实会被替换为现在的 __hello对象__ ，这就导致了无限嵌套。

要修改也很简单，添加一个新的引用，指向原来的hello对象，然后将这个新的引用放在实现类方法中就可以了：

```java
Hello raw = hello;
Hello nh = new Hello() {
    @Override
    public String hello() {
        return "I said " + raw.hello();
    }
};
hello = nh;
```

Easy!