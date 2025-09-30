---
title: MockData使用简述
tags:
  - 教程
published: true
cover: /images/AL52j9xg5.png
banner: /images/AL52j9xg5.png
abbrlink: 20735
date: 2023-02-12 14:11:17
category:
	- 技术
---
在以前我做开发的时候，经常会遇到需要向数据库中添加假数据的需求，有时又需要使用批量的随机数据来验证接口或是方法的稳定性以及容错测验。那个时候我还不知道有类似于 __[jmockdata](https://gitee.com/keko-boy/jmockdata)__ 或是 __[easy-random](https://github.com/j-easy/easy-random)__ 的数据生成工具，就只有傻傻地用姓名库和`for`循环来构造数据。

后来我知道了 __jmockdata__，也用过 __easy-random__，不过使用体验并不是很好。例如 __jmock__ 会复用对象导致循环引用时无法转`json`， __easy-random__ 构造自定义数据有点麻烦，总的来说它们和我预想的构建模式并不是很相符。我还是期望那种更加自由的、能凭直觉就定义数据构建方法的构建工具，比如 __weight__ 和 __height__ 能使用不同的随机方式，而不只是简单的范围随机。

{% ghcard Verlif/mockdata %}

而[mock-data](https://github.com/Verlif/mockdata)就实现了我的想法。例如实现基础的数据构建，就像下面这样：

```java
MockDataCreator creator = new MockDataCreator();
// 就像new Random.next一样，随机基础类型的数据
int i = creator.mock(int.class);
// 随机包装类型
int inte = creator.mock(Integer.class);
// 随机数组
int[] ints = creator.mock(int[].class);
// 指定数组大小
int[][] intsArray = creator.mock(new int[2][3]);
// 随机日期
Date date = creator.mock(Date.class);
```

不过这也是最基本的，构建自定义对象才是重点，毕竟这是用来做自定义数据的。我个人觉得这样的方式还是比较符合直觉的：

```java
// 创建数据构造器
MockDataCreator creator = new MockDataCreator();
// 获取构造器的当前配置
creator.getConfig()
        // 自动级联构建
        .autoCascade(true)
        // 只替换基础类型
        .appendFieldOption(FieldOption.IGNORED_VALUE | FieldOption.ALLOWED_PRIMITIVE)
// 通过类来实例化对象
Person person = creator.mock(Person.class);
// 或是手动实例化对象，然后填充数据
Person person = new Person();
creator.mock(person);
// 如果你只要其中的某个属性也可以
Pet pet = creator.mock(Person::getPet);
```

其中的`DoubleRandomCreator`实际上是实现的`DataCreator`接口，开发者可以通过实现这个接口来自定义构建方式：

```java
public interface DataCreator<R> extends TypeGetter {

    /**
     * 生成数据
     *
     * @param src     构建源信息。当此构造器绑定的类是通过{@link MockDataCreator#mock(Class)}的方式传入时，field为空。<br>
     *                例如此构造器绑定Person对象，而此时通过MockDataCreator.mock(Person.class)方式进入此构造器时，field对象为空。但其内部可能存在的Person则不会为空。<br>
     *                也就是说，只有在{@link MockDataCreator#mock(Class)}传入的类与此构造器绑定类相同时，返回的目标对象在构造时传入的field为空。
     * @param creator 当前所使用的数据创建器。
     * @return 生成的数据
     */
    R mock(MockSrc src, MockDataCreator.Creator creator);

    /**
     * 构建器匹配的类型列表，如果有特殊需求可以自己重写方法，在列表中放入匹配的类。这样构建匹配的类时就会调用这个构建器来构建
     */
    @Override
    default List<Class<?>> types() {
        List<Class<?>> list = new ArrayList<>();
        try {
            Method method = this.getClass().getMethod("mock", MockSrc.class, MockDataCreator.Creator.class);
            Class<?> type = method.getReturnType();
            list.add(type);
        } catch (NoSuchMethodException ignored) {
        }
        return list;
    }
}
```

所以实际上就只需要实现一个方法就可以了，`MockSrc`里面包括了目前的构建目标信息，便于开发者进行选择构建。__mock-data__ 也为接口构建提供了接口构建器，让`MockDataCreator`可以自动选择合适的实现类来完成接口的构造工作，内置的`List`、`Set`和`Map`就是使用的接口构建器，这样所有实现这三个接口的类（例如`ArrayList`、`HashSet`、`HashMap`等）都可以自动构建对应的对象，而不需要对它们重新设置。

当然，循环依赖生成的是独立对象，并且可以自定义每个类的循环深度。不过这些都是比较基础的功能，还不能达到我心中的便捷，毕竟如果需要构建出一个假的“真实”数据，开发者还是需要自己去设定每一个属性或是类的构建模型。虽然也可以通过结合 __JavaFaker__ 的方式解决，但不够优雅，所以在 __mock-data__ 在3.0版本加入了属性数据池模型，开发者只需要构建一个属性数据池，就可以让`MockDataCreator`优先使用数据池中的数据。这样做似乎与 __JavaFaker__ 很相似？并不是，属性数据池包括了三个目标： __类型（Class）__、__属性名（支持正则匹配）__、__数据池（数据对象数组）__。__类型__ 与 __属性名__ 用来指定 __数据池__ 的适用目标。使用方式就像下面这样：

```java
MockDataCreator creator = new MockDataCreator();
FieldDataPool dataPool = new FieldDataPool()
        // 自动识别同类型属性，包括int类型的所有名称中包含age的属性，忽略大小写，例如age、nominalAge
        .like(Person::getAge, 23, 24, 25, 26, 27)
        .next()
        // 添加FRUIT类的数据池，则会对所有的FRUIT类进行数据池选取，忽略名称
        .type(Person.FRUIT.class)
        .values(Person.FRUIT.APPLE)
        .next()
        // 对Date类的所有名称中能匹配`.*day`和`.*time`的属性进行数据池选取
        .type(Date.class)
        .values(new Date[]{new Date()}, ".*day", ".*time").next();
// 设置属性数据池
creator.fieldDataPool(dataPool);
// 开始构建
Person person = creator.mock(Person.class);
```

这样似乎也不优雅？确实，所以 __mock-data__ 实际上是提供了一个数据池框架，开发者完全可以通过继承`FieldDataPool`来构建配置文件填充的数据池。并且在 __mock-data__ 的*test*包中其实也实现了一个`properties`的数据池，只需要把数据池信息写在`properties`中就可以控制数据池填充了，就像这样：

```properties
int#age=[22, 23, 24, 25, 25, 27]
int#nominalAge=[23, 24, 25, 25, 27, 28]
double#height=[120, 130, 140, 150, 160, 170, 180, 190, 200]
double#weight=[40, 50, 60, 70, 80, 90, 100, 110, 120, 130, 140, 150, 160]
String#name=["假装这个名字", "这么长的姓名"]
String#nickname=["小明", "小红", "小张", "小丽"]
java.util.Date#birthday=["1998-01-08", "1998-03-08", "1998-05-08", "1998-07-08"]
```

详细内容可以看 [这里](https://verlif.top/mockdata/docs/4.x/FieldDataPool.html)。

也就是说，__mock-data__ 集成了完善的虚拟数据构建模型，并且泛型的兼容性与性能也还不错，具体可以看下下面的图片：

![构建测试](/images/1705990522343.png)

具体的测试内容可以把代码 __clone__ 下来，在*test*包下查看。