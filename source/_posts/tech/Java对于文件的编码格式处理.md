---
title: Java对于文件的编码格式处理
tags:
  - Java
  - 文件
published: true
abbrlink: 10020
date: 2022-02-27 10:06:52
category:
	- 技术
---
Java对于文件的读取或是写入一般都是使用stream流的方式，例如`FileInputStream`或是`FileOutputStream`。方式有很多种，每种方式都有自己的应用场景。

## 文件读取

一般情况下，我们使用Java读取文件内容使用的是以下方式:

```java
public String readFromFile(File file) {
    try (FileReader reader = new FileReader(file)) {
        char[] b = new char[1024];
        StringBuilder sb = new StringBuilder();
        int length;
        while ((length = reader.read(b)) > 0) {
            sb.append(b, 0, length);
        }
        return sb.toString();
    } catch (IOException e) {
        e.printStackTrace();
    }
    return null;
}
```

这种方式对于编码处理会有一些问题，使用的是`FileReader`的默认编码格式，具体是什么格式我还没研究过，之前我处理**UTF-8**的文件时出现了乱码的问题。后来我去查了下，要限定编码格式，可以通过以下方式:

```java
public String readFromFile(File file) {
    try (BufferedReader reader = new BufferedReader(
        new InputStreamReader(new FileInputStream(file), StandardCharsets.UTF_8)))
    {
        char[] b = new char[1024];
        StringBuilder sb = new StringBuilder();
        int length;
        while ((length = reader.read(b)) > 0) {
            sb.append(b, 0, length);
        }
        return sb.toString();
    } catch (IOException e) {
        e.printStackTrace();
    }
    return null;
}
```

这里其实就只是把`FileReader`换成了`BufferedReader`，不过核心是`InputStreamReader`，因为其可以设定读取文件的`InputStream`编码格式。

## 文件写入

文件写入也可以使用配套的`FileWrite`:

```java
public void writeToFile(String content, File target) throws IOException {
    try (FileWriter writer = new FileWriter(target)) {
        writer.write(content);
        writer.flush();
    }
}
```

同样的，`FileWriter`也是使用的默认的文件编码，可能会让中文字符变成乱码。这里也可以使用

```java
public void writeToFile(String content, File target) throws IOException {
        try (BufferedWriter writer = new BufferedWriter(
            new OutputStreamWriter(new FileOutputStream(target), StandardCharsets.UTF_8)))
        {
            writer.write(content);
            writer.flush();
        }
}
```

不过在写入文件时，如果文件已存在，那么`Writer`会根据文件编码来自动匹配编码。
