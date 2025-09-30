---
title: JavaDoc文档的书写
tags:
  - Java
  - JavaDoc
published: true
abbrlink: 16017
date: 2022-03-21 10:00:42
category:
	- 技术
---
这里只记录方法注释相关的语法或是tip。

doc文档注释主要是为了系统地查询这些类或是方法的说明。并且这类注释可以使用html标签来美化，常用的例如`<ul>`、`<strong>`等。

## 文档关键词表

| 关键词或匹配模式      | 说明                                          | 举例                                                               |
|---------------|---------------------------------------------|------------------------------------------------------------------|
| @author       | 标识一个类的作者                                    | @author description                                              |
| @deprecated   | 指名一个过期的类或成员                                 | @deprecated description                                          |
| {@docRoot}    | 指明当前文档根目录的路径                                | Directory Path                                                   |
| @exception    | 标志一个类抛出的异常                                  | @exception exception-name explanation                            |
| {@inheritDoc} | 从直接父类继承的注释                                  | Inherits a comment from the immediate surperclass.               |
| {@link}       | 插入一个到另一个主题的链接                               | {@link name text}                                                |
| {@linkplain}  | 插入一个到另一个主题的链接，但是该链接显示纯文本字体                  | {@linkplain name text} Inserts an in-line link to another topic. |
| @param        | 说明一个方法的参数                                   | @param parameter-name explanation                                |
| @return       | 说明返回值类型                                     | @return explanation                                              |
| @see          | 指定一个到另一个主题的链接                               | @see anchor                                                      |
| @serial       | 说明一个序列化属性                                   | @serial description                                              |
| @serialData   | 说明通过writeObject( ) 和 writeExternal( )方法写的数据 | @serialData description                                          |
| @serialField  | 说明一个ObjectStreamField组件                     | @serialField name type description                               |
| @since        | 标记当引入一个特定的变化时                               | @since release                                                   |
| @throws       | 和 @exception标签一样.                           | The @throws tag has the same meaning as the @exception tag.      |
| {@value}      | 显示常量的值，该常量必须是static属性。                      | Displays the value of a constant, which must be a static field.  |
| @version      | 指定类的版本                                      | @version info                                                    |

数据来源：[*菜鸟教程*](https://www.runoob.com/java/java-documentation.html)

这里的关键词中，以`@`开头的表示单行说明；以`{}`包裹的表示内联关键词，可放于任意位置。

## 一些注意事项

* 在doc注释中，`<>`会被识别为标签，无法显示于文档说明中。可以类似xml的书写方法，使用`&lt;`来代替`<`使得其不成为标签。