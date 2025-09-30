---
title: Android的阻塞对话框
tags:
  - Android
published: true
abbrlink: 52820
date: 2022-07-11 10:10:27
category:
	- 技术
---
今天有一个需求，就是要实现一个阻塞式的输入对话框，在对话框显示后，UI进程阻塞，随后在输入对话框点击确定后，返回输入的内容，然后继续UI进程。

本来我是试着用`wait()`和`notify()`来完成切换，但是很明显，这俩都在UI线程中，`wait()`就会一直wait，根本不会`notify`。然后我搜了半天，终于找到一个看起来靠谱的方法，用`loop()`来阻塞，用`RuntimeException`来结束loop。

我试了一下，完全可行。代码如下：

```java
    public synchronized String getText(String s) {
        final String[] s1 = {""};
        InputDialog id = new InputDialog(GameActivity.this) {
            @Override
            public void get(String str) {
                s1[0] = str;
                cancel();
                throw new RuntimeException();
            }
        };
        id.showAndGetText(s);
        try {
            Looper.loop();
        } catch(RuntimeException ignored) {
        }
        return s1[0];
    }
```
