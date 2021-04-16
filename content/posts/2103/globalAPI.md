---
title: "全域 API"
date: 2021-03-11
description: "Vue2.x 與 Vue3.x 差異 - 全域 API。"
keywords: ["Vue3", "Vue2.x 與 Vue3.x 差異"]
tags: ["Vue3", "Vue2.x 與 Vue3.x 差異"]
series: ["Vue3 筆記"]
image: images/covers/2103/globalAPI.png
draft: false
---

>本篇為官方文件 **[Migration from Vue 2](https://v3.vuejs.org/guide/migration/introduction.html)** 筆記。
- [全域 API](/posts/2103/globalapi/)
- [模板指令](/posts/2103/templatedirectives/)
- [元件 & 渲染函數](/posts/2103/componentsrenderfunction/)
- [自定義元素](/posts/2103/customelements/)
- [其他小改變](/posts/2103/other)
- [移除 API](/posts/2103/removedapi/)

<!--more-->

{{< featuredImage >}}

## 全域 API

Vue 2.x 的全域 Vue API 已更改為使用 **應用程式實體（application instance）**。

### 1. 2.x 語法

Vue 2.x 是透過 `Vue` 函式建立一個新的 Vue 實體：
```javascript
// Vue 2.x
const vm = new Vue({
  // 選項
});
```

並且有許多 **[全域 API](https://cn.vuejs.org/v2/api/#%E5%85%A8%E5%B1%80-API)** 和 **[全域配置](https://cn.vuejs.org/v2/api/#%E5%85%A8%E5%B1%80%E9%85%8D%E7%BD%AE)** 可以全域改變 Vue 的行為。例如，全域註冊：
```javascript
// Vue 2.x
Vue.component('my-component', MyComponent);
Vue.directive('my-directive', myDirective);
```

### 2. 全域 API 遇到的問題

Vue 2.x 這種宣告方式雖然方便，但也會導致一些問題。從技術上來說，Vue 2.x 並沒有「app」的概念（app 要互相獨立），只是透過 `new Vue()` 建立根 Vue 實體。當我們從 `Vue` 函式建立複數根實體，所有根實體將會共享全域環境配置。


### 3. createApp

為了避免上述問題，在 Vue 3.x 引入了 [`createApp`](https://v3.cn.vuejs.org/api/global-api.html#createapp)，用來取代 `new Vue()`。

呼叫 [`createApp`](https://v3.cn.vuejs.org/api/global-api.html#createapp) 函式回傳一個應用實體：
```javascript
// Vue 3.x
const app = Vue.createApp({
  // 選項
});
```

原本的全域 API 操作改在應用實體上操作：
```javascript
// Vue 3.x
const app = Vue.createApp({});
app.component('my-component', MyComponent);
app.directive('my-directive', myDirective);
```

應用實體大多數方法都會回傳自己，方便使用鏈式呼叫其它方法：
```javascript
// Vue 3.x
const app = Vue.createApp({})
  .component('my-component', MyComponent)
  .directive('my-directive', myDirective);
```

對應表：

|2.x 全域 API|3.x 應用實體 API (app)|
|---|---|
|[`Vue.config`](https://cn.vuejs.org/v2/api/??#全局配置)|[`app.config`](https://v3.cn.vuejs.org/api/application-api.html#config)|
|[`Vue.config.productionTip`](https://cn.vuejs.org/v2/api/?#productionTip)|移除|
|[`Vue.config.ignoredElements`](https://cn.vuejs.org/v2/api/?#ignoredElements)|[`app.config.isCustomElement`](https://v3.cn.vuejs.org/api/application-config.html#iscustomelement)|
|[`Vue.component`](https://cn.vuejs.org/v2/api/?#Vue-component)|[`app.component`](https://v3.cn.vuejs.org/api/application-api.html#component)|
|[`Vue.directive`](https://cn.vuejs.org/v2/api/?#Vue-directive)|[`app.directive`](https://v3.cn.vuejs.org/api/application-api.html#directive)|
|[`Vue.mixin`](https://cn.vuejs.org/v2/api/?#Vue-mixin)|[`app.mixin`](https://v3.cn.vuejs.org/api/application-api.html#mixin)|
|[`Vue.use`](https://cn.vuejs.org/v2/api/?#Vue-use)|[`app.use`](https://v3.cn.vuejs.org/api/application-api.html#use)|
|[`Vue.prototype`](https://cn.vuejs.org/v2/guide/plugins.html)|[`app.config.globalProperties`](https://v3.cn.vuejs.org/api/application-config.html#globalproperties)|

其他不會全域改變行為的全域 API 都通過 `exports` 導出，呼叫時需要手動導入，下節會提到。

### 4. 掛載

在 Vue 2.x 中，我們會使用 [`el`](https://cn.vuejs.org/v2/api/#el) 選項作用為掛載 Vue 實體與 DOM 元素的關係。
```javascript
// Vue 2.x
const vm = new Vue({
  el: '#app',
});
```

或者透過 [`$mount()`](https://cn.vuejs.org/v2/api/#vm-mount) 進行手動掛載。
```javascript
// Vue 2.x
const vm = new Vue({});

vm.$mount('#app');
```

而 Vue 3.x 沒有 `el` 選項，而是透過 [`mount`](https://v3.cn.vuejs.org/api/application-api.html#mount) 函式手動掛載根節點：
```javascript
// Vue 3.x
const app = Vue.createApp({
  // 選項
});

const vm = app.mount('#app');
```
`mount` 函式與大多數應用方法不同，回傳的是根元件實體，而非應用實體本身。

## 全域 API Treeshaking

Vue 3.x 對部分全域 API 實現了 [tree-shakable](https://developer.mozilla.org/zh-CN/docs/Glossary/Tree_shaking) 功能的支持。

>Tree shaking 是一個用於描述移除 JavaScript 上下文中的未引用程式碼（dead-code）行為的術語。它依賴於 ES2015 中的 `import` 和 `export` 語句，用來檢測程式碼模組是否被導出、導入，且被 JavaScript 檔案使用。

### 1. 2.x 語法

在 Vue 2.x 我們使用全域 API 並不需要手動導入，例如 `Vue.nextTick`：
```javascript
// Vue 2.x
import Vue from 'vue';

Vue.nextTick(() => {
  // something DOM-related
});
```

也就是說，有很多我們沒用到的全域 API 都會被一同打包，造成專案存在多餘的程式碼。

### 2. 3.0 語法

在 Vue 3.x 中，為了能支持 tree-shakable 功能，對全域和內部 API 都經過了重構，明確表示只能透過 ES Modules 構建的命名導出進行訪問。

```javascript
// Vue 3.x
import { nextTick } from 'vue';

nextTick(() => {
  // something DOM-related
});
```

### 3. 受影響的 API

Vue 2.x 中的這些全域 API 受此更改的影響：
- `Vue.nextTick`
- `Vue.observable`（用 `Vue.reactive` 替換）
- `Vue.version`
- `Vue.compile`（僅全構建）
- `Vue.set`（僅兼容構建）
- `Vue.delete`（僅兼容構建）

### 4. 內部 API

除了全域 API，Vue3.0 許多內部元件、幫助器也支持 tree-shaking。