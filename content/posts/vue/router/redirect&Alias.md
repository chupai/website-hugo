---
title: "Vue Router - 重新導向 & 別名"
date: 2020-11-03T05:00:00+08:00
description: "重新導向 & 別名。"
keywords: ["Vue","Vue Router","竹白"]
tags: ["Vue", "Vue Router"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/router/redirect&Alias.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## 重新導向

重新導向之前有稍微提到，也就是將當前路由轉向其它指定路徑路由。

設置 `redirect` 選項，當我們訪問 `/a` 時，URL 將會被替換成 `/b`，然後匹配路由也為 `/b`：
```javascript
const routes = [
  {
    path: '/a',
    redirect: '/b',
  },
  {
    path: '/b',
    // ...
  },
];
```
簡單來說就是 `/a` 會被重新導向至 `/b`。

**CodePen Demo：**[Vue 2.x - 重新導向](https://codepen.io/CHUPAIWANG/pen/QWEdXgR)

除了字串路徑，也可以傳遞路徑物件：
```javascript
redirect: { name: 'foo' }
```

或者是函式：
```javascript
redirect: to => {
  // 方法接收 目標路由 作為參數
  // return 重定向的 字串路徑/路徑物件
}
```

## 别名

使用 `alias` 別名是做什麼用的呢？

當我們訪問 `/b` 時，路徑會保持 `/b` 但，路由匹配則為 `/a`。

```javascript
const routes = [
  {
    path: '/a',
    alias: '/b',
    // ...
  },
];
```
這個功能讓你可以自由地將 UI 結構映射到任意的 URL，而不是受限於配置的巢狀路由結構。

**CodePen Demo：**[Vue 2.x - 别名](https://codepen.io/CHUPAIWANG/pen/BazpXqK)