---
title: "Vue Router - 模組化管理"
date: 2020-11-07
description: "模組化管理。"
keywords: ["Vue","Vue Router","竹白"]
tags: ["Vue", "Vue Router"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/router/module.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## 基本結構

路由配置基本結構：

```javascript
import Vue from 'vue';
import VueRouter from 'vue-router';
import Home from '../views/Home.vue';

Vue.use(VueRouter);

// 定義路由
const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home,
  },
  {
    // ...
  },
];

// 設定
const router = new VueRouter({
  mode: 'history',
  routes,
  scrollBehavior(to, from, savedPosition) {
    if (savedPosition) {
      return savedPosition;
    } else {
      return { x: 0, y: 0 };
    }
  },
});

// 守衛
router.beforeEach((to, from, next) => {
  next();
});

export default router;
```

## 模組化管理

如果專案越來越複雜，路由隨之變得越多，就需要將路由拆分，方便管理維護。

結構：
```
/router
├── index.js       # 配置檔
├── common.js      # 通用路由
|
└── /modules
    ├── index.js
    ├── home.js
    ├── user.js
    ├── ...
```


通用路由：
```javascript
// common.js

export default [
  {
    path: '/',
    redirect: '/index',
  },
  {
    path: '*',
    redirect: '/404',
  },
];
```

路由模組：
```javascript
// user.js

import User from '../../views/User.vue';

export default [
  {
    path: '/user',
    name: 'user',
    component: User,
  },
];
```

使用 ES6 展開運算子整合到配置檔：
```javascript
// ...

import home from './modules/home';
import user from './modules/user';
import error from './modules/error';
import common from './modules/common';

const routes = [
  ...home,
  ...user,
  ...error,
  ...common,
];
```

## 延遲加載

當我們專案到達一定程度時，打包後的 JavaScript bundle 會變得非常大，一次性加載，可能會花費較長時間，影響使用者體驗。

大多數情況下，使用者訪問網站時。並不需要立即從 JavaScript bundle 中取得全部的程式碼。

因此我們可以使用延遲加載拆分 bundle。webpack 在打包的過程中，非同步引入的資源會被打進單獨的 chunk 檔案，在需要執行該檔案時才會加載。

原本的寫法：
```javascript
import Foo from './Foo.vue';
```

改用延遲加載寫法：
```javascript
const Foo = () => import('./Foo.vue');
```


如果路由使用到多個元件，可以使用特殊的特殊的註釋語法
 **命名 chunk** 將它們打包載同一個 chunk 中：
```javascript
const Foo = () => import(/* webpackChunkName: "group-foo" */ './Foo.vue');
const Bar = () => import(/* webpackChunkName: "group-foo" */ './Bar.vue');
const Baz = () => import(/* webpackChunkName: "group-foo" */ './Baz.vue');
```