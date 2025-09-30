---
title: SpringBoot组件开发建议
tags: []
published: true
abbrlink: 22336
date: 2024-03-25 14:32:30
category:
	- 技术
---
我有时会去写一些Spring Boot相关的组件，用来给web应用提供服务，这些是我在开发过程中积累的一些技巧和建议。

## 组件服务开关

组件服务开关一般分为两种方式，**代码式**和**配置式**。

**代码式**的一般是由组件提供一个`EnableXXX`注解，当web服务使用了这个注解后，组件即为开启状态。

例如：

```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE})
@Documented
@Import({MockApiConfig.class})
public @interface EnableMockApi {
}
```

而**配置式**的一般是添加了组件依赖后，组件即为开启状态，通过配置中的`XXX.enabled=false`的方式关闭组件服务。

例如：

```java
@Configuration
@ConditionalOnProperty(prefix = "mockapi.data", value = "enabled", havingValue = "true", matchIfMissing = true)
public class YamlDataPool extends FieldDataPool {
    ......
}
```

两种方式各有利弊，但是我个人比较推荐**配置式**的，这种方式的侵入量会小很多，并且开关较为方便。

## 注入组件

对于三方项目来说，只是使用了基本的`@SpringBootApplication`注解或是`@ComponentScan`中未额外配置组件包名的，都没有办法对组件服务的工具进行注入，也就无法使用此组件。

要注入自己的组件当然可以使用`@ComponentScan`，在参数中增加组件包名，但是这样不优雅，我们还有更优雅的方式。

对于**代码式**开关的组件，可以在`EnableXXX`注解类代码中增加`@Import`注解，其中添加需要被Bean池管理的类，例如：

```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE})
@Documented
@Import({MockApiConfig.class})
public @interface EnableMockApi {
}
```

这里就把`MockApiConfig`进行了注入。多个组件的话需要开发者对组件的关系进行管理，在不同的类上添加不同的`Import`参数，避免都配置在一个类上。

对于**配置式**开关的组件来说，更推荐使用配置文件的方式。开发者需要在`resources`文件夹下增加两层文件夹`META-INF.spring`，然后再spring文件夹下增加一个文件`org.springframework.boot.autoconfigure.AutoConfiguration.imports`，文件名就是这么长。最后在文件中写入要注入的**全类名**即可，效果类似于`Import`的参数：

```text
idea.verlif.mockapi.config.MockApiConfig
idea.verlif.mockapi.config.MockApiBeanConfig
idea.verlif.mockapi.config.PathRecorder
```

像上面这样，我们就添加了三个类到Bean池中。

## 可替换组件

一般我们在开发一项给三方使用的服务或组件时，都会提供一些可更换的组件让三方开发的自定义性更高，通常我们建议使用**Configuration类**来管理这些可更换组件，并使用`ConditionalOnMissingBean`注解来让三方可以进行替换，例如：

```java
@Configuration
@ConditionalOnMockEnabled
@Import({MockApiBuilder.class, MockApiRegister.class, YamlDataPool.class})
public class MockApiBeanConfig {

    @Bean
    @ConditionalOnMissingBean(MockLogger.class)
    public MockLogger mockLogger() {...}
}
```

我们在组件里提供了默认的`MockLogger`，但它被`ConditionalOnMissingBean`标记，因此在实际项目中如果有自定义的继承或实现类存在于Bean池中，默认的`MockLogger`就会被替换成自定义的。这样开发者就可以通过自定义的`MockLogger`来自由替换默认的日志处理器，而不需要编写其他代码逻辑来手动切换。

这样做的好处是能使用Bean管理的方式切换实现类，充分利用Spring的AOC特性。

## 配置文件提示

很多组件都会使用配置文件的方式让组件的配置更方便，但是我们很容易发现，当组件打包变成依赖后，在实际项目中的配置文件中对组件的配置没有提示，甚至说找不到对应配置，即使配置没问出错并且可以生效。

因为配置文件的提示信息被放在了一个生成的文件中，需要我们使用`spring-boot-configuration-processor`这个依赖并开启：

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
```

随后重新编译我们的组件，你就会发现在`target/classes/META-INF`下文件夹下多了一个文件`spring-configuration-metadata.json`，这个文件就是配置元信息，里面的内容就像这样的：

```json
{
  "groups": [
    {
      "name": "mockapi",
      "type": "idea.verlif.mockapi.config.MockApiConfig",
      "sourceType": "idea.verlif.mockapi.config.MockApiConfig"
    },
    {
      "name": "mockapi.data",
      "type": "idea.verlif.mockapi.pool.YamlDataPool",
      "sourceType": "idea.verlif.mockapi.pool.YamlDataPool"
    }
  ],
  "properties": [
    {
      "name": "mockapi.data.enabled",
      "type": "java.lang.Boolean",
      "sourceType": "idea.verlif.mockapi.pool.YamlDataPool",
      "defaultValue": false
    },
    {
      "name": "mockapi.data.pool",
      "type": "java.util.List<idea.verlif.mockapi.pool.YamlDataPool$DataInfo>",
      "sourceType": "idea.verlif.mockapi.pool.YamlDataPool"
    },
    {
      "name": "mockapi.enabled",
      "type": "java.lang.Boolean",
      "description": "是否开启",
      "sourceType": "idea.verlif.mockapi.config.MockApiConfig",
      "defaultValue": false
    },
    {
      "name": "mockapi.path-strategy",
      "type": "idea.verlif.mockapi.config.MockApiConfig$PathStrategy",
      "description": "地址重复策略",
      "sourceType": "idea.verlif.mockapi.config.MockApiConfig"
    }
  ],
  "hints": []
}
```

此时我们再将组件放入实际项目中，再打开组件的配置时就会出现提示了，也可以使用配置跳转了。

## 开发建议

对于一个基本的组件来说，除了功能完善之外，还需要对其拓展性进行管理。通常拓展性包括两方面，一个是调用方式，一个是数据存储，例如下图：

![代码层级](/images/1712473188100.png)

### 调用方式

这里的调用接口，就是到达核心功能的入口，而**直调**就是最基础的入口。通常为了维护方便，调用接口只会有直调作为**唯一入口**。因为直调是唯一的入口，那么其他方式的调用就需要转换成直调方式，这就有了**调用转换器**，将其他的调用方式转换成直调方式。

例如经典的**Web后台**三层结构中，**service**层既是功能核心，又是调用转换器。service通过聚合其他service并提供多种调用方法的方式，拓展出不同的业务入口，提供给controller和其他service调用。

后来为了将核心层和调用转换器从**service**中剥离开，又衍生出了**biz**、**application**等层级。但是殊途同归，总的方向就是将核心功能与调用转换器功能解耦，编译开发和维护。

组件开发也是类似的，例如有一个求和的功能，除了提供基础方法`add`之外，还可以提供用于支持**socket**调用的`socketHandler`，用于支持通过启动参数直接求和的`commandHandler`，用于处理请求队列的`requestLine`等等。

### 数据解耦

数据解耦是个老生常谈的话题了，用过三方组件或应用的很多都会遇到“爱而不得”的情况（功能上完美符合需求，但是在存储上没法满足当前环境，比如只需要简单文件数据但只支持数据库存储、数据库选型不同）。这种情况常见于Web应用，为了应对这种情况，很多的框架就已经提供了仓储层了。通过接口或是注解的方式，对同一个存取需求做不同的实现。

这个话题就不必展开讲了，基本上开发者都是知道的，但是因为多一层结构的关系，导致很多项目或是服务没有做数据解耦的设计。

## 其他

本文使用到的代码由以下项目提供：

[mockapi](https://github.com/Verlif/mockapi) : 只需要一个注解即可为你的接口添加虚拟数据

参考资料：

[如何让springboot工程中自定义配置项在idea中提示](https://blog.csdn.net/u013202238/article/details/117407129)