---
title: Vue3项目支持离线加载
tags:
  - Vue3
  - 前端
category: 技术
cover: /images/pwa/applicationTip.png
banner: /images/pwa/applicationTip.png
date: '2025-10-11 8:43'
abbrlink: 43489
---

PWA（Progressive Web App，渐进式网页应用） 是一种融合了 网页（Web） 与 原生应用（Native App） 优点的技术。
简单来说，就是可以让你的网页从标签页面变成一个可以安装的应用，并且允许用户在没有网络连接的情况下使用。

<!-- more -->

## 技术简介

> PWA（Progressive Web App，渐进式网页应用） 是一种融合了 网页（Web） 与 原生应用（Native App） 优点的技术。
它让你用网页技术（HTML、CSS、JavaScript）构建出一个能像原生应用那样：
> - 可以安装到桌面或手机；
> - 能在离线或网络差的情况下运行；
> - 能接收推送通知；
> - 启动速度快，体验接近原生 App；
> - 并且依然是通过浏览器访问，不需要上架应用商店。
> 换句话说，PWA = 网页 + 应用体验。
>
> *—— ChatGPT*

简单来说，就是浏览器为你的网页提供了离线应用支持，它将你的网页所需资源缓存了下来，并拦截了页面请求，能匹配上这些资源的就直接存缓存中拿出来。

这里涉及到了两个重要的技术点：

1. Service Worker：它是一个在浏览器后台运行的脚本，负责拦截网络请求、缓存资源、处理推送通知等。
2. Manifest 文件：它是一个 JSON 格式的文件，用于描述你的 PWA 应用的名称、图标、启动 URL 等信息。

**Service Worker** 帮你实现离线使用与同步更新，**Manifest** 则告诉浏览器你配置的应用信息。

就像这样：

![applicationTip.png](/images/pwa/applicationTip.png)

右上角会出现一个安装应用的按钮，点击即可安装到桌面。

## 核心原理

PWA的核心原理是利用Service Worker来拦截网络请求，匹配到缓存中的资源就直接返回，否则就去请求服务器。

而PWA检测更新的原理更加简单，当使用PWA时，会生成一个类似于检验码的js文件，文件地址是固定的。就像md5校验码一样，浏览器会定期请求这个文件，如果与本地的文件有差异则表示此网页有新的版本。

## 简单开始

*以下内容弄个基于Vue3 + Vite进行说明。*

### 安装vite-plugin-pwa

`vite-plugin-pwa`是**Vite**提供的便利性插件，让你通过简单的配置即可完成离线应用。

```bash
npm install vite-plugin-pwa
```

### 配置PWA

在`vite.config.js`中添加如下配置：

```javascript
import { VitePWA } from 'vite-plugin-pwa'
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [
    vue(),
    VitePWA({
      registerType: 'autoUpdate', // 自动更新 service worker
      includeAssets: ['favicon.svg', 'favicon.ico', 'robots.txt', 'apple-touch-icon.png'],
      manifest: {
        name: 'My Vue App',
        short_name: 'VueApp',
        description: 'My awesome offline-ready Vue 3 app',
        theme_color: '#42b883',
        icons: [
          {
            src: 'pwa-192x192.png',
            sizes: '192x192',
            type: 'image/png'
          },
          {
            src: 'pwa-512x512.png',
            sizes: '512x512',
            type: 'image/png'
          }
        ]
      },
    })
  ]
})
```

**参数说明**

- **registerType**: 更新类型，包括两个值`['prompt' | 'autoUpdate']`，`prompt`表示让用户进行弹窗确认来升级，`autoUpdate`表示自动进行的升级。
- **includeAssets**: 你需要进行缓存的静态资源，比如favicon.ico、robots.txt等。
- **manifest**: 应用清单，也就是应用信息。这里需要特别注意`icons`参数，它需要真实的图片地址与分辨率，如有错误则不会在浏览器上显示应用安装按钮。

### 进行升级配置

在`main.js`中添加如下内容：

```javascript
import { registerSW } from 'virtual:pwa-register'

const updateSW = registerSW({
  onNeedRefresh() {
    if (confirm('有新版本可用，是否立即更新？')) {
      updateSW(true)
    }
  },
  onOfflineReady() {
    console.log('应用已准备好离线使用')
  }
})
```

这里的`updateSW`定义是为了让开发者根据需要来进行手动升级，开发者可以通过调用`updateSW(true)`来手动进行页面更新。
你也可以不进行定义，这样只能等待用户手动关闭页面并重新打开才能完成新页面的载入。

## 小提示

- 如果你是使用的类似于`vite dev`的开发模式，那么离线应用是不会生效的，你需要先对应用进行打包，然后再使用`vite preview`的方式进行预览。
- 你可以打开f12，随后刷新网页来看一下是否缓存成功了（出现ServiceWorker则表示成功了）：

    ![f12.png](/images/pwa/f12.png)
