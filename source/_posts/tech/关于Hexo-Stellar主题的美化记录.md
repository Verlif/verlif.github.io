---
title: 关于Hexo-Stellar主题的美化记录
tags:
  - 教程
  - Hexo
  - 美化
category: 技术
abbrlink: 40282
date: 2024-05-01 02:22:28
---
美化是一项无穷无尽的工作。
<!-- more -->

这两天把博客迁到了Hexo中进行管理，然后用上了Stellar主题。

默认的主题样式自然是不能满足我们的要求，所以站点美化~~迫在眉睫~~马上开始。

## 基本的配置

首先就是用上Stellar的基本配置来美化， 也就是Stellar提供的内容。

一般我们会在项目目录下新建一个（或是从`themes/stellar/_config.yml`文件复制）`_config.stellay.yml`，然后在这里面进行参数修改。

之所以要用这样的方式而不是直接修改主题目录下的配置文件（`themes/stellar/_config.yml`），是为了在未来主题更新的时候可能出现的文件变动不会覆盖掉我们自己的更改。

这里的修改 [文档](https://xaoxuu.com/wiki/stellar/theme-settings/) 上还是比较详细的，文件中也有注释说明，就不多说了。

## 自定义CSS

自定义的css有很多的方式，比较通用的方式是通过配置文件外联注入样式文件。

1. 在`source`目录下新建一个`css`文件夹。
2. 在`css`文件夹下新建一个样式文件，比如`stellar.css`
3. 在`_config.stellay.yml`中增以下代码：

    ```yml
    inject:
      head:
        - <link rel="stylesheet" href="/css/stellar.css?1">
    ```

然后我们再编辑`stellar.css`的内容，例如这样：

```css
/* 中间内容的高度修复 */
#main {
    background: #4e4d4fe6;
    border-radius: 4px;
    margin: 8px;
    padding: 0;
    height: fit-content;
}

/* 文章列表卡片鼠标悬浮样式 */
:root:not([data-theme]) .post-list .post-card {
    transition-duration: 200ms;
    background: #ffffff24
}
:root:not([data-theme]) .post-list .post-card:hover {
    box-shadow: 0 0 4px -2px #ffffff, 0 0 24px -8px #ffffff;
    background: -webkit-linear-gradient(right, rgba(0, 0, 0, 0) 10%, rgb(0 0 0 / 60%) 60%);
}

/* 文章列表卡片字体 */
.l_main .post-list .post-title {
    border-bottom-style: dotted;
    border-color: #5d5a5b;
    font-weight: bold;
    padding-bottom: 10px;
}

/* 导航栏未被选中的文字 */
.navbar nav a {
    color: rgb(221, 221, 221);
}
```

## 主题内置样式修改

这里涉及到主题内文件的修改，建议主题加载方式换成手动fork仓库，然后用自己fork下的仓库文件，避免主题更新导致的文件覆盖。

比如标签云后的数字与标签名称是一样的，导致了看起来很奇怪，我们可以修改`themes/stellar/source/css/_components/widghts/tagcloud.styl`来更改标签统计数字的样式：

```styl themes/stellar/source/css/_components/widghts/tagcloud.styl
.widget-wrapper.tagcloud .widget-body
  border-radius: $border-card
  padding: 12px 16px
  background: var(--alpha50)
  a  
    word-break: break-word
    color: var(--text-p2)
    line-height: 1.5
    &:hover
      color: $color-hover
    span
      opacity: 0.4
```

这里我们给了统计数字一个透明度，这样将数字与文字作了颜色区分：

![tagcloud](/images/hexo_stellar_style/tag_cloud.png)

其他的样式修改同理，不做赘述。