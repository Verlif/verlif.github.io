---
title: 一些实用的css字体特效
tags:
  - css
category: 技术
abbrlink: 38177
date: 2024-04-29 10:24:22
---

<!-- more -->

## 黑幕

简单地说，就是将字体隐藏，只有把鼠标移上去<span class="mask">才会显示内容</span>。

这个其实就是一个简单的hover：

```css
.mask {
  background-color: #252525;
  color: #252525;
  transition: color 0.5s;
  padding: 0 2px
}
.mask:hover {
  color: #ffffff;
}
```

上面的样式表示，`class`为`mask`的内容，背景和字体同色为`#252525`，并使用了**0.5秒**的过渡效果。当鼠标移上去时，字体改色为`#ffffff`。

所以我们就可以这样使用：

```html
233<span class="mask">你们看不到我</span>233
```

## 字体跳动

<span class="spring">当鼠标移上去后</span>，<span class="spring">字体就会跳动</span>，<span class="spring">这行字的也可以哦</span>，<span class="spring">你可以试试</span>。

这里也是用的简单的`top`属性，就像这样：

```css
.spring {
  position: relative;
  top: 0px;
  transition-duration: 200ms;
}
.spring:hover {
  top: -4px;
}
```

将内容样式的`position`设置成`relative`，方便我们设置`top`属性，然后通过`hover`将`top`设置成负数而将字体上移。注意`top`不能设置过大，否则字体容易滑出鼠标位置，导致字体回落。