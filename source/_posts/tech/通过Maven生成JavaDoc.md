---
title: 通过Maven生成JavaDoc
tags:
  - Java
  - Maven
  - JavaDoc
published: true
abbrlink: 11435
date: 2022-05-25 10:02:30
category:
	- 技术
---
一般来说在代码中使用`javadoc`方式的注释就会方便二次开发，但是对于三方依赖来说，可查询的文档更重要。一般情况下可以有两种方式进行选择，一者可以通过信息发布的方式进行文档撰写，二者可以通过`javadoc`的方式自动生成。我比较懒，所以采用的第二种方式。

## 通过`javadoc`指令

通过使用以下指令来生成项目的doc文档：

```shell
javadoc -d javadocs -encoding utf-8 -charset utf-8 -sourcepath F:\Code\Java\project\src -subpackages idea.verlif.project -version -author
```

参数说明：

- `d` - 文档生成路径，从当前位置开始
- `encoding` - 编码方式
- `charset` - 编码格式
- `sourcepath` - 项目文件地址，从src开始
- `subpackages` - 起始的包名，这里会递归进行文档生成
- `version` - 生成文档版本
- `author` - 添加作者信息

## 通过插件

`Maven`提供了`maven-javadoc-plugin`插件来自动生成JavaDoc文档。只需要在`pom.xml`配置中添加以下插件即可：

```xml

<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-javadoc-plugin</artifactId>
    <version>3.0.0</version>
    <configuration>
        <doclint>none</doclint>
        <reportOutputDirectory>javadocs</reportOutputDirectory>
        <destDir>just-simmand</destDir>
    </configuration>
</plugin>
```

插件配置：

- `doclint` - 文档格式校验方式。因为`Java1.8`中在文档生成时会校验文档的完整有效性，如果缺少什么`return`或是`param`参数什么的，就会报错并无法生成文档。这里设置为`none`则不会校验。
- `reportOutputDirectory` - 表示了文档的生成目录
- `destDir` - 文档归档名称，也就是文档的根目录名，与`reportOutputDirectory`结合就是会将文档生成在当前项目下的`javadocs/just-simmand`中。

### 文档编码格式

我一般都是写的中文文档，所以需要将文档的编码格式设置为utf-8，这里就需要在properties中添加两条配置：

```xml

<properties>
    <maven.compiler.encoding>UTF-8</maven.compiler.encoding>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
</properties>
```