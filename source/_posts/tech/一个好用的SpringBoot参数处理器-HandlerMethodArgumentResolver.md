---
title: 一个好用的SpringBoot参数处理器-HandlerMethodArgumentResolver
tags:
  - SpringBoot
abbrlink: 14314
date: 2024-05-13 16:58:46
category: 技术
---

<!-- more -->

今天查资料，偶然看到了一篇介绍处理分页参数的文章 - [我再也不想写@RequestParam("current")了](https://juejin.cn/post/7360497239871094824)。

我本来想着处理分页参数不是用封装的方式就可以了吗，但是想着万一人家有更加隐式的方式呢，就点进去看了一下。

果然，里面介绍了一个我没有用过的接口- `HandlerMethodArgumentResolver`。

这个类就很有意思，可以拦截接口参数，并修改入参值。

那我们就直接开始。

## 接口源码

源码非常简单，一看就知道怎么用的了：

```java
public interface HandlerMethodArgumentResolver {
    boolean supportsParameter(MethodParameter parameter);

    @Nullable
    Object resolveArgument(MethodParameter parameter, @Nullable ModelAndViewContainer mavContainer, NativeWebRequest webRequest, @Nullable WebDataBinderFactory binderFactory) throws Exception;
}
```

首先`supportsParameter`就是判断接口参数是否需要进行处理，也就是执行`resolveArgument`方法。这里的入参`MethodParameter`就包含了调用的方法和处理的参数。

值得注意的是，一般情况下，`supportsParameter`对于一个入参都只会调用一次。当`supportsParameter`被调用后，`HandlerMethodArgumentResolverComposite`就会将其存放在`argumentResolverCache`中，用以作下次调用的缓存。

`resolveArgument`就是对这个入参进行处理，入参就是一堆请求信息，而出参就是对参数的设定值。

## 举例

我们知道`@RequestParam`可以设定默认值，那么对于`@RequestBody`该怎样设定默认值呢？可以这样：

```java

public class MyHandlerMethodArgumentResolver implements HandlerMethodArgumentResolver {

    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        // 只对被@RequestBody注解标记的参数进行默认值注入
        return parameter.hasParameterAnnotation(RequestBody.class);
    }

    @Override
    public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
        Object value = getValue(parameter, webRequest);
        // TODO: modify value
        return value;
    }
    
    private Object getValue(MethodParameter parameter, NativeWebRequest request) {
        // TODO: 从request解析出parameter对应的值
        return null;
    }
}
```

## 使用

当然，我们只定义了一个参数处理器是不够的，SpringBoot并不会直接把这个实现类拿来用，我们还需要手动把实现类添加到处理器列表中：

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
        resolvers.add(new MyHandlerMethodArgumentResolver());
    }
}
```

值得注意的是，如果你用的`@RequestBody`，你会发现参数解析并不会执行到我们自定义的方法里。这是因为`RequestMappingHandlerAdapter`适配器的控制。

这里不展开讲，只说明一点，我们自定义的处理器在默认27个处理器的倒数，也就是只有在其他处理器不处理的情况下才会执行我们的处理器。因此，我们可以采用这样的方式来提升我们处理器的优先级：

```java
@Configuration
public class MockArgInitializer implements InitializingBean {

    @Autowired
    private RequestMappingHandlerAdapter requestMappingHandlerAdapter;
    @Autowired
    private MockArgResolver argResolver;

    @Override
    public void afterPropertiesSet() throws Exception {
        // 这里通过反射将MockArgResolver添加到第一位
        Field argumentResolvers = RequestMappingHandlerAdapter.class.getDeclaredField("argumentResolvers");
        HandlerMethodArgumentResolverComposite resolverComposite = (HandlerMethodArgumentResolverComposite) FieldUtil.getFieldValue(requestMappingHandlerAdapter, argumentResolvers);
        if (resolverComposite != null) {
            List<HandlerMethodArgumentResolver> resolvers = (List<HandlerMethodArgumentResolver>) FieldUtil.getFieldValue(resolverComposite, "argumentResolvers");
            if (resolvers != null) {
                resolvers.add(0, argResolver);
            }
        }
    }
}
```

不通过默认的`WebMvcConfigurer`，而是通过反射的方式手动控制顺序。至于为什么要用反射，你可以查看`RequestMappingHandlerAdapter.getArgumentResolvers`的源码就知道了。

## 其他

从`WebMvcConfigurer`可以看出，里面其实还有很多的add方法，例如`addReturnValueHandlers`就是对应的更改方法返回值。

这里有一个基于`HandlerMethodArgumentResolver`的项目，可以参考下：[mockapi-arg](https://github.com/Verlif/mockapi/tree/master/mockapi-arg)