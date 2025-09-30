---
title: 简述JDK中的ServiceLoader（服务注册机制）
tags:
  - Java
category: 技术
abbrlink: 65058
date: 2024-12-06 14:38:00
---

技术是工具，不是目的，我们学习的目的是让我们在达成目的前，有更多的选择。

<!-- more -->

我就说能在知乎上学到东西吧（5毛1条，括号删掉）。

先放个链接吧，感谢[程序员木木熊](https://www.zhihu.com/question/486985113/answer/48179300848)。

## 服务注册模式

在使用一项技术或是规范前，我们得知道我们是否有使用它的必要。

实际上，对于使用者来说，服务注册模式与模块化类似，我们的一个功能实现并不会具体描述细节，而是通过解耦注册的方式来使用不同的逻辑实现。

那我们什么时候需要呢？通常就是在我们进行适配型功能开发的时候会使用。（容灾分压的需求暂时不在本文的讨论范围内）

更具体来说，类似于数据库调用、文件存储、数据解析等需要接入其他服务的功能开发时，我们可以通过技术手段来方便地进行处理逻辑变更。

## 接口设计

首先我们知道一点，抽象是解耦基础，而对于Java来说，我们通常会使用接口来进行逻辑抽象化处理。

举例：

假设我需要一个翻译，而大多数的翻译都是单语言翻译，那么我就可以定义一个翻译接口：

```java
public interface Translator {

    /**
     * 翻译
     *
     * @param words 待翻译的句子
     * @return 翻译后的句子
     */
    String translate(String words);
}
```

然后我们就可以~~用这个接口愉快地翻译了~~开始找能提供翻译的服务提供方了。

## 服务实现

假设我们有两门语言需要进行翻译：

*   A语言，在每句话前面要说`什么？`来启动，比如`吃了吗？没吃的话吃我一拳`就需要翻译成`什么？吃了吗？没吃的话吃我一拳`
*   B语言，在每句话后面要加上`嘤嘤嘤`来结尾，比如`你这厮莫不是来消遣洒家`就需要翻译成`你这厮莫不是来消遣洒家，嘤嘤嘤`

当然啦，现在经济下行了，没钱来找别人服务了，只能我们自己动手了，于是我们就有了以下的实现类：

A语言翻译：

```java
public class ATranslator implements Translator {
    @Override
    public String translate(String words) {
        return "什么？" + words;
    }
}
```

B语言翻译：

```java
public class BTranslator implements Translator {
    @Override
    public String translate(String words) {
        return words + "，嘤嘤嘤";
    }
}
```

## 使用服务

我们既然都有了服务实现了，我们是不是可以就这样进行使用呢？

```java
Translator translator = new ATranslator();
System.out.println(translator.translate("V我50"));
```

的确可以，但是很明显，如果我们需要使用B语言翻译的时候，我们就需要把代码换成B语言实现类了。这里只有一个地方使用还好改，但如果多个地方使用呢？

当然，哪怕是我们多个地方使用，我们都可以通过单例模式、工厂模式这些设计模式来减少修改量，也可以使用Spirng框架来管理注入。但是如果我们需要更动态的扩展呢？

现在的情况是，我们的翻译有更专业的三方翻译，且在不同的地方（环境）需要用到不同的翻译。那么我们最好的方式就是使用配置模型来切换翻译实现，例如配置文件或是切换外挂组件的方式。

## ServiceLoader

### 配置服务提供者

现在我们就来使用ServiceLoader。

首先，我们需要告诉ServiceLoader我们有哪些服务提供商可以选择，于是我们就在`resources`目录下创建一个文件夹`META-INF.services`。

*   META-INF是存放各种元数据的文件夹，里面通常是对这个jar包的描述信息。
*   services是ServiceLoader管理的服务信息文件夹，里面就是存放各类服务的描述文件了。

然后我们就要对我们的翻译接口进行描述了，这里我们需要创建一个名为`idea.verlif.tool.Translator`的文件。很明显，这个文件名是我们翻译接口的全类名，ServiceLoader就是通过文件名来对应服务的。

然后在`idea.verlif.tool.Translator`文件中写下我们的两个翻译实现：

```idea.verlif.tool.Translator
idea.verlif.tool.ATranslator
idea.verlif.tool.BTranslator
```

可以发现我们每一行就是一个实现类的全类名。

### 使用服务

有了提供商的信息，我们就可以开始使用服务了。

```java
ServiceLoader<Translator> translators = ServiceLoader.load(Translator.class);
for (Translator driver : translators) {
    System.out.println(driver.translate("你好呀"));
}
```

于是我们可以得到以下结果：

```text
什么？你好呀
你好呀，嘤嘤嘤
```

可以看到我们通过`ServiceLoader.load(Translator.class)`来加载服务实例管理对象。

值得注意的是，`ServiceLoader`是实现了`Iterable`接口的，因此我们可以直接进行遍历。遍历的顺序其实就是我们配置文件中实现类的书写顺序。

## 更适合的使用方式

看起来似乎多此一举，我们既然能在配置文件中写明服务提供商，那为什么不直接用呢？因为我们目前只是在同项目中进行的服务注册，但在实际项目中，我们应该是以额外组件或是依赖的方式进行注册的。

此刻，我们新建一个项目，并新建目录`idea.verlif.tool`，同时将Translator接口复制于此。随后新建第三个C语言翻译类：

```java
public class CTranslator implements Translator {
    @Override
    public String translate(String words) {
        return "桀桀桀，" + words;
    }
}
```

同样的，我们在新建项目中增加服务提供商的信息文件（META-INF/services/idea.verlif.tool.Translator），并加入新建翻译类的全类名。

打包新项目，并在之前的项目中引用打包好的新项目jar包。再次运行这段代码：

```java
ServiceLoader<Translator> translators = ServiceLoader.load(Translator.class);
for (Translator driver : translators) {
    System.out.println(driver.translate("你好呀"));
}
```

此时结果就是：

```text
什么？你好呀
你好呀，嘤嘤嘤
桀桀桀，你好呀
```

奉上截图：

![ServiceLoaderDemo](/images/ServiceLoaderDemo.png)

## 总结

`ServiceLoader`是jdk1.6引入的服务加载器，旨在模块化服务。实际上只要你点开`ServiceLoader`的源码，其上的注释就已经说明了其作用与用法了。

网上老是说Java是过度设计，从某种方面来说的确是这样的，但这和环境有关系。就像是现在都还有一大堆的公司喜欢写`Service`接口，但实际上他们只会用到一种实现方式。

技术是工具，不是目的，我们学习的目的是让我们在达成目的前，有更多的选择。
