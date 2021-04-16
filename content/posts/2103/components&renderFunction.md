---
title: "元件 & 渲染函數"
date: 2021-03-13
description: "Vue2.x 與 Vue3.x 差異 - 元件 & 渲染函數"
keywords: ["Vue3", "Vue2.x 與 Vue3.x 差異"]
tags: ["Vue3", "Vue2.x 與 Vue3.x 差異"]
series: ["Vue3 筆記"]
image: images/covers/2103/components&renderFunction.png
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

## 函數式元件 Functional Component

**函數式元件** 基本上就是一個無狀態（沒響應式資料，無實體 `this`）、不屬於任何生命週期的一種元件。

在 Vue 2.x 中，主要有兩種作用：
1. 作為性能優化，因為初始化速度比有狀態元件快
2. 回傳多個根節點

然而，在 Vue 3.x 中，有狀態元件的性能已經提高到可以忽略不計的程度，再加上有狀態元件也包含回傳多個根節點的能力。

因此，函數式元件在 Vue 3.x 唯一的用處就是建立簡單元件。

### 1. 2.x 語法

使用渲染函數 `render`，需要設置 `functional` 選項：
```javascript
// Vue 2.x 
Vue.component('dynamic-heading', {
  functional: true,
  props: {
    // ...
  },
  render(h, context) {
    // ...
  },
};
```

若使用單檔案元件 `.vue` 的模板，需要再 `<template>` 內加上 `functional`：
```vue
<!-- Vue 2.x -->
<template functional>
  <!--  ...  -->
</template>
```

### 2. 3.x 語法

在 Vue 3 中，所有函數式元件都是使用普通函式建立的，也就是說，不需要定義 `functional: true` 選項了。

```javascript
// Vue 3.x
import { h } from 'vue';

const DynamicHeading = (props, context) => {
  // ...
};

export default DynamicHeading;
```


## 渲染函數 Render Functions

**渲染函數** 的變動，不影響使用 `<template>` 的開發者。

- `h` 從 Vue 2.x 的參數傳遞，改為全域導入。
- 渲染函數參數的更動，使狀態元件和函數式元件之間更加一致。
- vnode 現在有一個扁平的 prop 結構。

### 1. 參數

在 2.x 中，`render` 函式將自動接收 `h` 函式作為參數（`h` 為 `createElement` 的常規別名）：
```javascript
// Vue 2.x
export default {
  render(h) {
    // ...
  },
};
```

在 3.x 中，`h` 現在是全域導入，而不是作為參數自動傳遞。
```javascript
// Vue 3.x
import { h } from 'vue';

export default {
  render() {
    // ...
  },
};
```
`render` 函式不再接收任何參數。

### 2. VNode Props 結構

```javascript
// 2.x
{
  class: ['button', 'is-outlined'],
  style: { color: '#34495E' },
  attrs: { id: 'submit' },
  domProps: { innerHTML: '' },
  on: { click: submitForm },
  key: 'submit-button'
}
```

```javascript
// 3.x 語法
{
  class: ['button', 'is-outlined'],
  style: { color: '#34495E' },
  id: 'submit',
  innerHTML: '',
  onClick: submitForm,
  key: 'submit-button'
}

```

## 非同步元件 Async Components

- 新的 `defineAsyncComponent` 輔助方法，用於顯式地定義非同步元件。
- `component` 選項重命名為 `loader`。
- Loader 函式本身不再接收 `resolve` 和 `reject` 參數，且必須回傳一個 Promise。

### 1. 2.x 語法

在 Vue 2.x 中，**非同步元件**是透過動態導入某個元件檔案來定義的：
```javascript
// Vue 2.x
const asyncPage = () => import('./components/NextPage.vue');
```

帶有選項的高階語法：
```javascript
// Vue 2.x
const asyncPage = {
  component: () => import('./components/NextPage.vue'),
  delay: 200,
  timeout: 3000,
  error: ErrorComponent,
  loading: LoadingComponent,
};
```

### 2. 3.x 語法

在 Vue 3.x，由於函數式元件被定義為純函式，因此**非同步元件**需要透過 [`defineAsyncComponent`](https://v3.cn.vuejs.org/api/global-api.html#defineasynccomponent) 輔助方法顯式地定義：
```javascript
// Vue 3.x
import { defineAsyncComponent } from 'vue';

// 不帶選項的非同步元件
const asyncPage = defineAsyncComponent(() => import('./components/NextPage.vue'));

// 帶選項的非同步元件
const asyncPageWithOptions = defineAsyncComponent({
  loader: () => import('./components/NextPage.vue'),
  delay: 200,
  timeout: 3000,
  errorComponent: ErrorComponent,
  loadingComponent: LoadingComponent,
});
```

另外，`component` 選項被重新命名為 `loader`，且不再接收 `resolve` 和 `reject` 參數，必須始終回傳 Promise：
```javascript
// 2.x 
const oldAsyncComponent = (resolve, reject) => {
  /* ... */
};

// 3.x 
const asyncComponent = defineAsyncComponent(
  () =>
    new Promise((resolve, reject) => {
      /* ... */
    }),
);
```

## emits 選項

### 1. 自定義事件

在 Vue 3.x 中，元件的自定義事件 `$emit` 必須在 [`emits`](https://v3.cn.vuejs.org/api/options-data.html#emits) 選項中宣告：
```vue
<!-- Vue 3.x -->
<template>
  <div>
    <p>{{ text }}</p>
    <button v-on:click="$emit('accepted')">OK</button>
  </div>
</template>
<script>
export default {
  props: ['text'],
  emits: ['accepted'],
};
</script>
```

因此 Vue 3.x 移除了 `.native` 修飾符號，任何未在 `emits` 選項中宣告的 event，將被自動加到 `$attrs`。而 `attrs` 預設情況下是綁定到根元件的。

### 2. 驗證拋出的事件

與 prop 型別驗證類似，如果使用物件語法定義 event，可以用來驗證。

將為 event 分配一個函式，該函式接收傳遞給 `$emit` 呼叫的參數，並回傳一個布林值以指示 event 是否有效。

```javascript
// Vue 3.x
app.component('CustomForm', {
  emits: {
    // 沒有驗證
    click: null,

    // 驗證submit 事件
    submit: ({ email, password }) => {
      if (email && password) {
        return true;
      } else {
        console.warn('Invalid submit event payload!');
        return false;
      }
    },
  },
  methods: {
    submitForm() {
      this.$emit('submit', { email, password });
    },
  },
});
```