---
title: SpringBoot的Validation校验
tags:
  - Java
  - SpringBoot
published: true
abbrlink: 48662
date: 2022-06-16 10:09:45
category:
	- 技术
---
在使用SpringBoot进行web开发时，经常性地需要进行参数值校验，比如某某字段不能为空，某某数字不能小于多少。一般情况下，我们都会使用`Validation`来进行自动校验。这里就说一些`Validation`相关的东西。

## 依赖

在SpringBoot中，使用`Validation`非常简单，官方已经整合了`hibernate-validator`校验，都不需要手动配置即可完成大部分工作。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

## 使用

### 分组

先说分组。比如对于同一个类，我们在进行创建与更新这两个操作时，需要校验的字段与方式可能会不同。这时我们会用到`group`分组。

比如：

```java
@NotNull(groups = Insert.class)
private Long id;
```

这个分组会被`@Validated(Insert.class)`所校验，但不会被`@Validated`或`@Validated(Update.class)`校验。这里的`Insert.class`只是一个标记接口，用来表示分组而已。

另外，用来分组的接口类支持继承关系。也就是说，如果有一个接口`InsertExt.class`是继承自`Insert.class`，那么`@NotNull(groups = Insert.class)`也是会被`@Validated(Insert.class)`校验的。

### 级联校验

有时我们需要对一个对象中的另一个对象或是集合中的对象进行校验，就需要用到`@Valid`注解。

例如：

```java
@Valid
private List<User> userList;
```

这样，`Validation`才会对`userList`中的对象进行逐个校验。

### 校验模式

__hibernate-validator__ 支持两种校验模式：快速校验与全量校验。

- 快速校验 - 当校验到有一个错误时即停止校验并返回错误
- 全量校验（默认） - 校验所有标记，并返回所有的错误信息

可以通过配置类来修改：

```java
@Configuration
public class ValidatorConfiguration {
    @Bean
    public Validator validator(){
        ValidatorFactory validatorFactory = Validation
            .byProvider(HibernateValidator.class)        
            .configure()        
            .failFast(true)        
            .buildValidatorFactory();
        return validatorFactory.getValidator();
    }
}
```

### 自定义校验

自定义校验只需要两步：

1. 添加自定义注解
2. 添加校验方法

举个例子，比如我们需要校验枚举类可以这样写：

```java
/**
 * 枚举类限制检测
 *
 * @author Verlif
 * @version 1.0
 * @date 2021/12/20 10:30
 */
@Target({ElementType.FIELD, ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Constraint(validatedBy = EnumValid.EnumValueValidator.class)
public @interface EnumValid {

    /**
     * 允许的枚举类name() - String。<br/>
     * 当有值时，{@linkplain #blocked()}无效
     */
    String[] allowed() default {};

    /**
     * 禁止的枚举类name() - String。
     * 当{@linkplain #allowed()}有值时，此方法无效
     */
    String[] blocked() default {};

    /**
     * 是否允许空值。（默认 - true）
     */
    boolean nullable() default true;

    /**
     * 错误提示
     */
    String message();

    Class<?>[] groups() default {};

    Class<? extends Payload>[] payload() default {};

    /**
     * 枚举类参数验证器
     */
    class EnumValueValidator implements ConstraintValidator<EnumValid, Object> {

        private EnumValid enumValid;

        @Override
        public void initialize(EnumValid enumValid) {
            this.enumValid = enumValid;
        }

        @Override
        public boolean isValid(Object o, ConstraintValidatorContext context) {
            if (o == null) {
                return enumValid.nullable();
            }
            String key;
            if (o instanceof Enum<?>) {
                key = ((Enum<?>) o).name().toUpperCase(Locale.ROOT);
            } else {
                key = o.toString().toUpperCase(Locale.ROOT);
            }
            String finalKey = key;
            if (enumValid.allowed().length > 0) {
                return Arrays.stream(enumValid.allowed()).anyMatch(s -> s.toUpperCase(Locale.ROOT).equals(finalKey));
            } else {
                return Arrays.stream(enumValid.blocked()).noneMatch(s -> s.toUpperCase(Locale.ROOT).equals(finalKey));
            }
        }
    }
}
```

可以看到，`EnumValid`是我们的自定义注解，用来标记需要校验的属性并设置校验参数。其中有我们必须要添加的三个方法：

- String message();
- Class<?>[] groups() default {};
- Class<? extends Payload>[] payload() default {};

`message`方法返回的是校验失败提示语，`groups`方法表示了校验分组，`payload`我还暂时不知道是干什么的。

在`EnumValid`中写的参数都是为了校验方法中使用，也就是我们下面要说的`EnumValueValidator`。

另外，我们有一个很重要的注解`@Constraint(validatedBy = EnumValid.EnumValueValidator.class)`，这个表示了我们写的这个注解会被哪个类来校验。`EnumValueValidator.class`就是我们的校验类。

`EnumValueValidator`是怎么写的我就不多说了，一看就能看懂。比较重要的是`ConstraintValidator<EnumValid, Object>`的两个泛型。第一个泛型表示了传过来的校验注解，这里对应了`EnumValid`，第二个泛型表示了校验的参数类型，比如你只想校验`String`，那就给一个`String`类。

## 捕获错误

由`Validation`校验失败产生的异常是`MethodArgumentNotValidException`，我们可以通过全局捕获此异常来进行自定义操作，例如返回前端可读的错误提示。

通常我会用 [exception-spring-boot-starter](https://github.com/Verlif/exception-spring-boot-starter) 来解决这个问题。

[![Release](https://jitpack.io/v/Verlif/exception-spring-boot-starter.svg)](https://jitpack.io/#Verlif/exception-spring-boot-starter)

```xml
<dependency>
    <groupId>com.github.Verlif</groupId>
    <artifactId>exception-spring-boot-starter</artifactId>
    <version>2.6.6-0.2</version>
</dependency>
```

比如使用了`exception-spring-boot-starter`，那就非常简单了，只需要新建一个异常处理类即可：

```java
@Component
public class MethodArgumentNotValidExceptionHolder implements ExceptionHolder<MethodArgumentNotValidException> {

    @Override
    public Class<MethodArgumentNotValidException> register() {
        return MethodArgumentNotValidException.class;
    }

    @Override
    public BaseResult<?> handler(MethodArgumentNotValidException e) {
        BindingResult result = e.getBindingResult();
        StringBuilder sb = new StringBuilder();
        if (result.hasErrors()) {
            List<ObjectError> errors = result.getAllErrors();
            errors.forEach(p -> {
                FieldError fieldError = (FieldError) p;
                sb.append(fieldError.getField()).append(" - ").append(fieldError.getDefaultMessage()).append(", ");
            });
        }
        if (sb.length() >= 2) {
            return new BaseResult<>(ResultCode.FAILURE_PARAMETER).withParam(sb.substring(0, sb.length() - 2));
        } else {
            return new BaseResult<>(ResultCode.FAILURE_PARAMETER);
        }
    }
}
```

这里的`BaseResult`是我用来传递给前端的数据包，可以替换为自己的。这样就会给出可读的错误提示了，不然就是一大串的没有必要的错误信息。

## 常用的内置注解表

| 验证注解                                 | 验证的数据类型                                                                        | 说明                                                                            |
|--------------------------------------|--------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| @AssertFalse                         | Boolean,boolean                                                                | 验证注解的元素值是false                                                                |
| @AssertTrue                          | Boolean,boolean                                                                | 验证注解的元素值是true                                                                 |
| @NotNull                             | 任意类型                                                                           | 验证注解的元素值不是null                                                                |
| @Null                                | 任意类型                                                                           | 验证注解的元素值是null                                                                 |
| @Min(value=值)                        | BigDecimal，BigInteger, byte,short, int, long，等任何Number或CharSequence（存储的是数字）子类型 | 验证注解的元素值大于等于@Min指定的value值                                                     |
| @Max(value=值)                        | 和@Min要求一样                                                                      | 验证注解的元素值小于等于@Max指定的value值                                                     |
| @DecimalMin(value=值)                 | 和@Min要求一样                                                                      | 验证注解的元素值大于等于@ DecimalMin指定的value值                                             |
| @DecimalMax(value=值)                 | 和@Max要求一样                                                                      | 验证注解的元素值小于等于@ DecimalMax指定的value值                                             |
| @Digits(integer=整数位数, fraction=小数位数) | 和@Min要求一样                                                                      | 验证注解的元素值的整数位数和小数位数上限                                                          |
| @Size(min=下限, max=上限)                | 字符串、Collection、Map、数组等                                                         | 验证注解的元素值的在min和max（包含）指定区间之内，如字符长度、集合大小                                        |
| @Past                                | java.util.Date,java.util.Calendar;Joda Time类库的日期类型                             | 验证注解的元素值（日期类型）比当前时间早                                                          |
| @Future                              | 与@Past要求一样                                                                     | 验证注解的元素值（日期类型）比当前时间晚                                                          |
| @NotBlank                            | CharSequence子类型                                                                | 验证注解的元素值不为空（不为null、去除首位空格后长度为0），不同于@NotEmpty，@NotBlank只应用于字符串且在比较时会去除字符串的首位空格 |
| @Length(min=下限, max=上限)              | CharSequence子类型                                                                | 验证注解的元素值长度在min和max区间内                                                         |
| @NotEmpty                            | CharSequence子类型、Collection、Map、数组                                              | 验证注解的元素值不为null且不为空（字符串长度不为0、集合大小不为0）                                          |
| @Range(min=最小值, max=最大值)             | BigDecimal,BigInteger,CharSequence, byte, short, int, long等原子类型和包装类型           | 验证注解的元素值在最小值和最大值之间                                                            |
| @Email(regexp=正则表达式,flag=标志的模式)      | CharSequence子类型（如String）                                                       | 验证注解的元素值是Email，也可以通过regexp和flag指定自定义的email格式                                  |
| @Pattern(regexp=正则表达式,flag=标志的模式)    | String，任何CharSequence的子类型                                                      | 验证注解的元素值与指定的正则表达式匹配                                                           |
| @Valid                               | 任何非原子类型                                                                        | 指定递归验证关联的对象，级联验证                                                              |