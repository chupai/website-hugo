---
title: "Vue Router - 基礎"
date: 2020-11-01T04:00:00+08:00
description: "Vue Router 是 Vue.js 官方的路由管理器。頁面的路徑改變就是元件的切換。"
keywords: ["Vue","Vue Router","竹白"]
tags: ["Vue", "Vue Router"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/router/base.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## 前端路由概述

>前端路由為 **單頁應用（SPA）** 的基礎標配。

路由這個概念來自後端伺服器，透過不同的路徑，回傳不同的網頁內容，每次請求都會刷新頁面，因此會不定時間的空白畫面。

隨著 AJAX 技術的盛行，資料請求不再需要刷新頁面，開始有前後端分離的開發模式，前端只需要接收後端傳遞的資料，再將內容渲染出來。各種 MVVM 框架的興起，前端發展逐漸走向 SPA，由於只有單一個 html，一但加載完成，並不會因為使用者的操作而重新載入或跳轉，取而代之的是利用 JS 動態切換不同的元件，改變 URL 但頁面整體不刷新。

前端路由是 URL 與畫面之間的關係，有兩種實現方式 history 和 hash，詳細可以參考這篇文章：**[Web 前端路由原理解析和实现](https://github.com/whinc/blog/issues/13)**。

## Vue Router

>[Vue Router](https://router.vuejs.org/zh/)

Vue Router 是 Vue.js 官方的路由管理器。頁面的路徑改變就是元件的切換。

### 1. CDN
```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/vue-router/3.4.5/vue-router.min.js"></script>
```

### 2. Vue CLI 專案

安裝：
```javascript
npm install vue-router --save
```

配置檔：
- Vue CLI3 為 `router.js`
- Vue CLI4 為 `/router/index.js`

```javascript
import Vue from 'vue';
import Router from 'vue-router';
import Home from './views/Home.vue';

Vue.use(Router);

export default new Router({
  routes: [
    {
      path: '/',
      name: 'home',
      component: Home,
    },
    {
      path: '/about',
      name: 'about',
      component: () => // 路由懶加載 lazy-loading
        import('./views/About.vue'),
    },
  ],
});
```

將 `router` 掛載到 Vue 實體：
```javascript
import Vue from 'vue';
import App from './App.vue';
import router from './router';  // 引入配置檔

Vue.config.productionTip = false;

new Vue({
  router,  // 載入
  render: h => h(App),
}).$mount('#app');
```


## 基本範例

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/vue-router/3.4.5/vue-router.min.js"></script>

<div id="app">
  <nav>
    <!-- 導航元件 -->
    <router-link to="/">Home</router-link> |
    <router-link to="/about">About</router-link>
  </nav>
  <p>path: {{ $route.fullPath }}</p>
  
  <!-- 路由配置元件 -->
  <router-view />
</div>
```
`<router-link>` 與 `<router-view>` 為 Vue Router 提供的元件：
- `<router-link>` 預設為 `<a>`，透過 `to` 特性用於指定跳轉的連結；
- 而 `<router-view>` 是負責掛載路由匹配到畫面的元件。

```javascript
// 路由元件
const Home = { template: '<div><h1>Home</h1></div>' };
const About = { template: '<div><h1>About</h1></div>' };

// 配置路由
const routes = [
  { path: '/', component: Home },
  { path: '/about', component: About },
];

// 建立 router 實體，並傳 routes 配置
const router = new VueRouter({ routes });

// 建立實體 + 掛載 router
const vm = new Vue({
  el: '#app',
  router,
});
```

**CodePen Demo：**[Vue 2.x - Vue Router 基本範例](https://codepen.io/CHUPAIWANG/full/MWydeGX)

## 配置路由

配置路由映射，元件和路徑映射的關係。

[`routes`](https://router.vuejs.org/zh/api/#routes) 為一個陣列，僅能接受 RouteConfig 設定資料物件，這個物件本身包含了以下基本屬性：
- `path`：對應的虛擬路徑，從 `base` 開始算。
- `component`：路由配置的 Vue 元件。

```javascript
const routes = [
  { 
    path: '/',
    component: Home
  },
  { 
    path: '/about',
    component: About
  },
];
```


其他屬性之後會慢慢提到。

## 路由連結

### 1. router-link 元件

[`<router-link>`](https://router.vuejs.org/zh/api/#router-link) 元件，預設會解析成 HTML 的 `<a>` 標籤，有兩個屬性可設定：
- [`tag`](https://router.vuejs.org/zh/api/#tag)：指定標籤。
- [`to`](https://router.vuejs.org/zh/api/#to)：導向的路徑字串。

```html
<router-link to="/">Home</router-link>
<router-link to="/about">About</router-link>
```

`to` 除了接受路徑字串，也可以使用 `v-bind` 來綁定一個物件。點擊 `<router-link>` 會在內部呼叫路由實體 `push` 方法，更多詳細用法會在 **[程式控制](/posts/vue/router/programmaticnavigation/)** 說明。


### 2. active 樣式

對應的路由匹配成功時，`<router-link>` 會自動添加一個 [`active-class`](https://router.vuejs.org/zh/api/#active-class) 類別。

可以使用 `.router-link-active` 來定義樣式：
```css
.router-link-active {
  /* style */
}
```

或是透過 `active-class` 特性，各別引入自定義類別名稱：
```html
<router-link to="/" active-class="is-active">Home</router-link>
```
```css
.is-active {
  /*  style  */
}
```

如果不想要各別引入自定義類別名稱，可以設置 Router 的[`linkActiveClass`](https://router.vuejs.org/zh/api/#linkactiveclass) 選項：
```javascript
// router.js
export default new Router({
  linkActiveClass: 'is-active', // 預設值 'router-link-active'
  // ...   
});
```

### 2. 精確匹配模式

要注意，當前路由的所有父級都會添加 `active-class`，舉例：`/about` 就包含 `/` 也添加 `active-class`。

而精確匹配的 `<router-link>` 還會添加一個 [`exact-active-class`](https://router.vuejs.org/zh/api/#exact-active-class)，用法與 `active-class` 相同，Router 選項為 [`linkExactActiveClass`](https://router.vuejs.org/zh/api/#linkexactactiveclass)（預設值 `'router-link-exact-active'`）。

你可以改用 `.router-link-exact-active` 來定義 active 樣式，就可以避免父級也套用 active 樣式。

或者使用 [`exact`](https://router.vuejs.org/zh/api/#exact) 精確匹配模式：
```javascript
<router-link to="/" exact>Home</router-link>
```
使用精確匹配模式，只有路由完全匹配時才會添加 `active-class`。

**CodePen Demo：**[Vue 2.x - Vue Router active class](https://codepen.io/CHUPAIWANG/pen/OJNexja)


## 路由渲染

[`<router-view>`](https://router.vuejs.org/zh/api/#router-view) 路由配置元件，會渲染當前路徑指定的元件。如果要設置複數 `<router-view>`，有 `name` 特性可以設置名稱。

因為它也是個元件，所以可以配合 `<transition>` 和 `<keep-alive>` 使用。

## 路由實體 & 路由物件

### 1. 路由實體

在 Vue 實體內，可以透過 `$router` 獲取路由實體，我們能透過它來呼叫路由方法。

### 2. 路由物件

另外，還可以透過 [`$route`](https://router.vuejs.org/zh/api/#路由对象) 獲取當前的路由資訊，包含了當前 URL 解析得到的資訊。

這個物件 **唯讀**，可用於監聽變化。部分屬性之後會提到，詳細屬性可以先參考 **[文件 API](https://router.vuejs.org/zh/api/#路由对象属性)**。
