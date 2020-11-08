---
title: "Vuex - 注意事項"
date: 2020-10-29
description: "Vuex 注意事項。"
keywords: ["Vue","Vuex","竹白"]
tags: ["Vue", "Vuex"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/vuex/warning.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## 專案結構

Vuex 並不限制程式碼結構，只需要遵守以下幾點規則：
1. 應用層級的狀態應該集中到單個 `store` 物件中。
2. 提交 `mutation` 是唯一更改狀態的唯一方法，且必須是同步操作。
3. 非同步操作應該封裝到 `action` 中。

### 1. 基本結構

單一個 `store.js` 檔案：
```javascript
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);

export default new Vuex.Store({
  strict: process.env.NODE_ENV !== 'production',
  state: {},
  mutations: {},
  actions: {},
  getters: {},
});
```

### 2. 資料夾管理

```
/store
├── index.js
├── actions.js
├── mutations.js 
├── ...
└── /modules
    ├── moduleA.js
    └── moduleB.js
```

```javascript
// index.js

import Vue from 'vue';
import Vuex from 'vuex';
import mutations from './mutations';
import actions from './actions';
import getters from './getters';

Vue.use(Vuex);

export default new Vuex.Store({
  state: {},
  mutations,
  actions,
  getters,
  modules: {
    moduleA,
    moduleB,
  }
});
```

**官方提供範例：**[shopping-cart](https://github.com/vuejs/vuex/tree/dev/examples/shopping-cart)

模組部分也可以使用資料夾拆分的更細去管理。


## 嚴格模式

一邊的情況下，進行以下非法操作：使用非 `mutation` 修改狀態與使用 `mutation` 進行非同步更新狀態，並不會拋出錯誤，但在 devtool 中無法正確捕捉到狀態。

因此我們可以開啟 **嚴格模式**：
```javascript
const store = new Vuex.Store({
  strict: true,
  // ...
});
```

這樣當我們進行非法操作時，變會拋出錯誤訊息提示，保證所有狀態能夠被 devtool 追蹤。


### 1. 環境

但有一點必須注意，因為嚴格模式會深度監測狀態樹，所以在 **發布環境** 下避免使用，避免不必要的性能損失。

因此我們可以使用 **[環境變數](https://cli.vuejs.org/zh/guide/mode-and-env.html#环境变量和模式)** 來處理：
```javascript
const store = new Vuex.Store({
  strict: process.env.NODE_ENV !== 'production',
});
```

## v-model

如果我們要將取回來的狀態使用 `v-model` 綁定，就會遇到 `v-model` 綁定計算屬性的問題。計算屬性預設無法回寫（會跳出警告），再加上會直接違反 Vuex 直接修改狀態的規則。

因此我們需要用到計算屬性的 setter：
```javascript
// Component.vue

computed: {
  message: {
    get() {
      return this.$store.state.message;
    },
    set(value) {
      this.$store.commit('updateMessage', value);
    },
  },
}
```
```javascript
// store.js

state: {
  message: '',
},
mutations: {
  updateMessage(state, message) {
    state.message = message;
  }
}
```

**CodePen Demo：**[Vue 2.x -Vuex v-model](https://codepen.io/CHUPAIWANG/pen/dyXpGwX)

## 解決狀態丟失的問題

套件：
- [vuex-persist](https://github.com/championswimmer/vuex-persist#readme)
- [vuex-along](https://github.com/boenfu/vuex-along)
