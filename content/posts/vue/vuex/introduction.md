---
title: "Vuex - 起手式"
date: 2020-10-26
description: "Vuex 是一個專為 Vue.js 應用程式開發的狀態管理模式。"
keywords: ["Vue","Vuex","竹白"]
tags: ["Vue", "Vuex"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/vuex/introduction.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## 什麼是 Vuex

官方說明：
>Vuex 是一個專為 Vue.js 應用程式開發的狀態管理模式。它採用集中式存儲管理應用的所有元件的狀態，並以相應的規則保證狀態以一種可預測的方式發生變化。

簡單來說，Vuex 就是一個狀態倉庫，將各個元件公用的狀態放到裡面進行全域統一管理，只要倉庫內的狀態發生變化，引用它的元件都會自動更新。

## 為什麼要用 Vuex

### 1. 元件之間狀態資料傳遞的方式

元件之間的狀態資料傳遞：
- 父元件傳子元件：Props
- 子元件傳父元件：Events
- 兄弟元件之間的傳遞：EventBus

但當我們遇到多個元件共享相同狀態資料，或者大範圍的傳遞，狀態資料的傳遞與維護就會變得相當麻煩。

### 2. Vuex 用處

Vuex 將元件的共享狀態抽取出來，將狀態變成全域的，方便集中管理。在這種模式下，我們的元件樹構成了一個巨大的「視圖」，不管在樹的哪個位置，任何元件都能獲取狀態或者觸發行為。

{{< notice warning >}} 
當專案規模到達一定程度時，才適合使用 Vuex，否則可能是繁瑣冗餘的。
{{< /notice >}}

### 3. Flux、Redux、MobX

Vuex 是 Vue.js 專用的狀態管理模式，其概念是來自 [Flux](https://github.com/facebook/flux) 架構思想，使用類似概念的還有 [MobX](https://github.com/mobxjs/mobx) 和常用於 React.js 的 [Redux](https://github.com/reduxjs/redux)。

這裡就不多做說明，有興趣的可以參考以下文章：
- [Vuex、Flux、Redux、Redux-saga、Dva、MobX](https://zhuanlan.zhihu.com/p/53599723)
- [Flux、Redux、Vuex、MobX 總結 - 概念篇](https://zhuanlan.zhihu.com/p/75696114)

## Vue CLI 專案

安裝：
```
npm install vuex --save
```

導入、建立 `store` 物件：
```javascript
// store.js
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);

export default new Vuex.Store({
  state: {},
  mutations: {},
  actions: {},
});
```
當專案更複雜時，通常會改用資料夾結構管理，之後會提到。

將 `store` 物件掛載到 Vue 實體：
```javascript
// main.js
import Vue from 'vue';
import App from './App.vue';
import store from './store';

Vue.config.productionTip = false;

new Vue({
  store,
  render: h => h(App),
}).$mount('#app');
```

## Promise 

Vuex 依賴 Promise。如果你支持的瀏覽器並沒有實現 Promise（比如 IE），那麼你可以使用一個 polyfill 的庫，例如 [es6-promise](https://github.com/stefanpenner/es6-promise)。