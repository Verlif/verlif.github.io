---
title: 写Android的那点事
tags:
  - 二三事
  - Android
published: true
abbrlink: 44989
date: 2022-06-26 09:58:20
category:
	- 吐槽
---
## Parameter.getAnnotation

本来我对写Android没什么感想的，直到今天，遇到了一个很莫名其妙的bug。

本来我是想用Java写一个框架，然后通过jitpack来引入gradle依赖。本来我想的是大家都是Java，你Android应该不会说什么吧。然后我先是遇到了第一个问题：

__fastjson2会在Android上报错。__

因为我用的是2.0.7版本的，当时我还看到了2.0.7.android，我还在想为什么要把Android独立出来。现在我明白了，因为Android的一些限制导致了必须要分版本来控制方法。后面我把框架的依赖改成了2.0.7.android就可以了。

然后当我以为一切都很美好的时候，我运行框架指令时就报错了。没有错误信息，直到我debug进去，才发现是Parameter.getAnnotation方法没有进去。并且有些方法指令可以加载，有些就不行。我debug了一个小时都没看出来问题出在哪里。然后我就放弃了，想着这个项目就这样吧，人生无望了，啊啊啊啊啊啊啊啊。然后就在刚才，我才想起，之前没问题的是因为Parameter.getAnnotation返回的是null，而出错的是有注解的，那如果我去掉注解的话，是不是就可以了呢？然后我在框架上开了个Android分支，去掉了注解，打包依赖，重新运行，OK，OHHHHHHHHHHHHHHHHHHH！

所以说，Android，你凭什么要出问题？？？？？？？！！！！！！！
