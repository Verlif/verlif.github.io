---
title: 在Java中提取字符串中的区域数据
tags:
  - Java
  - 文本解析
published: true
abbrlink: 48555
date: 2022-02-25 10:05:35
category:
	- 技术
---
这里的区域数据指的是类似于`Hello #{name}`中的`#{name}`一样，是一段连续的字符串。

之前有一个需要将字符串中的宏变量替换的需求，而宏变量可以自定义，写在数据库中。所以需求变成了：

> 提取字符串中所有的宏变量，然后将宏变量进行替换

这里宏变量用的是花括号`{}`框起来的，并且其中的内容只有大写字母与小数点，并不会存在相互包含的关系，所以我一开始想用正则匹配去处理。但是字符串中的宏变量位置、数量都不确定 ~~(最主要是因为正则我不熟悉)~~。最后只能简单粗暴地对字符串进行遍历了。

这里的遍历思路其实很简单，因为宏变量是用成对的括号括起来的，所以可以以`{`为始，`}`为终，将中间的所有字符提取出来就是需要的宏变量了。

```java
/**
 * 获取字符串中的所有宏变量。<br/>
 * 因为没有其他的提取需求，所以就直接将左右括号定死了。<br/>
 *
 * @param s 需要提取宏变量的字符串
 * @return  字符串中的所有宏变量
 */
public List<String> macro(String s) {
    // 获取字符串的字符数组
    char[] chars = s.toCharArray();
    // 构造宏变量列表
    List<String> list = new ArrayList<>();
    // 用StringBuilder作为宏变量储存的临时容器
    StringBuilder sb = new StringBuilder();
    // 开始遍历
    for (char c : chars) {
        // 这里做的是分步处理，逻辑上更清晰。也可以将几个if统一起来，减少代码量
        // 当出现左括号时，表示此处是宏变量的头
        if (c == '{') {
            sb.append(c);
        // 出现有括号时，表示此处是宏变量的尾。此时可以存入宏变量列表并清空容器
        } else if (c == '}') {
            sb.append(c);
            list.add(sb.toString());
            // 清空StringBuilder
            sb.delete(0, sb.length());
        // 以容器的内容作为是否出于宏变量位置的标识
        } else if (sb.length() > 0) {
            sb.append(c);
        }
    }
    return list;
}
```

这个方法适用于没有包含关系的带有头尾标识的字符串提取，还是挺简单的。
