---
title: "Vue Router - 路由元件傳遞"
date: 2020-11-04T04:00:00+08:00
description: "路由元件傳遞。"
keywords: ["Vue","Vue Router","竹白"]
tags: ["Vue", "Vue Router"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/router/passingProps.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## 路由元件傳遞

如果我們直接在元件內使用 `$router` 會使之與其對應路由形成高度耦合，那麼該元件就只能用於特定的 URL 上使用，限制了其靈活性。

舉例來說，如果直接在元件內透過 `$router` 取得路徑參數：
```javascript
// component
const User = {
  template: '<div>User {{ $route.params.id }}</div>',
};

// router
const router = new VueRouter({
  routes: [{ path: '/user/:id', component: User }],
});
```

那麼這個 `User` 元件就只能用於特定路徑參數的 URL。

## Props

我們可以改用 `props` 傳入路徑參數。

如果 `props` 選項被設置為 `true`，`$route.params` 將會被設置為元件的屬性：
```javascript
// component
const User = {
  props: ['id'],
  template: '<div>User {{ id }}</div>',
};

// router
const router = new VueRouter({
  routes: [
    { 
      path: '/user/:id',
      component: User,
      props: true
    },
  ],
});
```
這樣子我的元件的 `id` 就不一定只能透過 `$router` 取得。


**CodePen Demo：**[Vue 2.x - 路由元件傳遞 props](https://codepen.io/CHUPAIWANG/pen/vYKxOwm)

如果使用命名視圖，會對應到多個元件，因此需要分別設置：
```javascript
const router = new VueRouter({
  routes: [
    {
      path: '/user/:id',
      components: { default: User, sidebar: Sidebar },
      props: { default: true, sidebar: false },
    },
  ],
});
```

### 1. 物件模式

除了上述的布林模式，我們也可以傳遞物件，它會被按原樣設置為元件屬性。當 `props` 是靜態的時候有用。

```javascript
// component
const Foo = {
  props: ['boo'],
  template: '<div>{{ boo }}</div>',
};

// router
const router = new VueRouter({
  routes: [
    { 
      path: '/foo',
      component: Foo,
      props: { boo: 'boo' },
    },
  ],
});
```

**CodePen Demo：**[Vue 2.x - 路由元件傳遞 props 物件模式](https://codepen.io/CHUPAIWANG/pen/eYzvpep)

### 2. 函式模式

如果使用函式模式，我們可以透過參數取得 `route` 物件：
```javascript
const router = new VueRouter({
  routes: [
    {
      path: '/user/:id',
      component: User,
      props: (route) => ({
        id: route.params.id,
      }),
    },
  ],
});
```

**CodePen Demo：**[Vue 2.x - 路由元件傳遞 props 函式模式](https://codepen.io/CHUPAIWANG/pen/JjKWWdM)