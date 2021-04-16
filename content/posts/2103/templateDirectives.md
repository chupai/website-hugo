---
title: "模板指令"
date: 2021-03-12
description: "Vue2.x 與 Vue3.x 差異 - 模板指令"
keywords: ["Vue3", "Vue2.x 與 Vue3.x 差異"]
tags: ["Vue3", "Vue2.x 與 Vue3.x 差異"]
series: ["Vue3 筆記"]
image: images/covers/2103/templateDirectives.png
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

## 元件上的 v-model 指令

Vue 3.x 語法變化概述：
- 用於自定義元件時，`v-model` prop 和 event 預設名稱已更改：
  - prop：`value` -> `modelValue`；
  - event：`input` -> `update:modelValue`。
- `v-bind` 的 `.sync` 修飾符號和元件的 `model` 選項已移除，使用 `v-model` 搭配參數即可替代。
- 現在可以在同一個元件上使用多個 `v-model` 進行雙向綁定。
- 現在可以自定義 `v-model` 修飾符號。

### 1. 2.x 語法

在 Vue 2.x 的元件上使用 `v-model` 等同綁定 `value` prop 和 `input` 事件：
```html
<!-- Vue 2.x -->
<my-component v-model="pageTitle"></my-component>

<!-- 是以下的簡寫: -->
<my-component
  :value="pageTitle"
  @input="pageTitle = $event"></my-component>
```

若不希望使用預設的 `value` 和 `input` 事件，可以使用 `model` 選項來設定 prop 和 event 名稱，避免不同的元件上的語意衝突。

```javascript
// Vue 2.x 
Vue.component('base-checkbox', {
  model: {
    prop: 'checked',
    event: 'change',
  },
  props: {
    checked: Boolean,
  },
  template: `
    <input
      type="checkbox"
      v-bind:checked="checked"
      v-on:change="$emit('change', $event.target.checked)"
    >
  `,
});
```

如果不只一個 prop 需要雙向綁定，就需要借助另一個與 `v-model` 非常相似的 `v-bind.sync`。詳細說明可以參考 **[Vue2.x - sync 修飾符號](https://cn.vuejs.org/v2/guide/components-custom-events.html#sync-修饰符)**。

### 2. 3.x 語法

由於 `v-bind.sync` 與 `v-model` 過於相似，Vue 3.x 將兩者結合，移除了 `v-bind` 的 `sync` 修飾符號，現在可以使用多個 `v-model` 指令。

元件 `v-model` 指令中的 prop 和 event 預設名稱也已更改，避免與需要用到 `value` prop 的標籤衝突：
- prop：`value` -> `modelValue`；
- event：`input` -> `update:modelValue`。

```html
<!-- Vue 3.x -->
<my-component v-model="pageTitle"></my-component>

<!-- 是以下的簡寫: -->
<my-component 
  :modelValue="pageTitle"
  @update:modelValue="pageTitle = $event"></my-component>
```
但要注意的是，Vue 3.x 新增了 `emits` 選項，除了 prop 需要宣告還有 event。

若要修改預設名稱，將不再使用 `model` 選項，而是將一個參數傳遞給 `model`：
```html
<!-- Vue 3.x -->
<my-component v-model:title="pageTitle"></my-component>

<!-- 是以下的簡寫: -->
<my-component 
  :title="pageTitle"
  @update:title="pageTitle = $event"></my-component>
```

**CodePen Demo：**[Vue 3.x - v-model 參數](https://codepen.io/CHUPAIWANG/pen/BaQVOyJ)

使用多個 `v-model` 指令：
```html
<!-- Vue 3.x -->
<my-component v-model:title="pageTitle" v-model:content="pageContent"></my-component>

<!-- 是以下的簡寫： -->
<my-component 
  :title="pageTitle"
  :content="pageContent"
  @update:title="pageTitle = $event"
  @update:content="pageContent = $event"
></my-component>
```

在 Vue 3.x 除了可以使用內建修飾符號，還可以自定義修飾符號。

添加到元件 `v-model` 的修飾符號會透過 `modelModifiers` prop 提供給元件。`modelModifiers` prop 預設為空物件，當 `v-model` 使用自定義修飾符號時，`modelModifiers` 會包含自定義修飾符號名稱屬性，舉例來說 `v-model.capitalize=""` 就會是 `{ capitalize: true }`。

接著我們就可以在事件處理函式中檢查是否有使用自定義修飾符號。

**CodePen Demo：**[Vue 3.x - v-model 修飾符號](https://codepen.io/CHUPAIWANG/pen/LYbgerY)

對於帶參數的 `v-model`，自定義修飾符號生成的 prop 將會是 `arg + "Modifiers"`，例如 `v-model:description.capitalize=""`，生成 prop 名稱為 `descriptionModifiers`。

## key attribute

- 對於 `v-if`/`v-else`/`v-else-if` 的各分支項 `key` 將不再是必須的，因為現在 Vue 會自動生成唯一的 `key`。
  - 如果你手動提供 `key`，那麼每個分支必須使用唯一的 `key`。你不能通過故意使用相同的 `key` 來強制重用分支。
- `<template v-for>` 的 `key` 應該設置在 `<template>` 標籤上（而不是設置在它的子節點上）。

### 1. 條件分支

在 Vue 2.x 時，當我們使用 `v-if` 相關指令時，會需要使用 `key`，來獨立 DOM 狀態。
```html
<!-- Vue 2.x -->
<div v-if="condition" key="yes">Yes</div>
<div v-else key="no">No</div>
```

而 Vue 3.x 則會自動生成唯一的 `key`：
```html
<!-- Vue 3.x -->
<div v-if="condition">Yes</div>
<div v-else>No</div>
```

因此不建議再手動提供 `key`，若你手動提供，變得每個分支都需要設置 `key`。

### 2. \<template> 結合 v-for 指令

在 Vue 2.x 中 `<template>` 標籤不能擁有 `key`。不過你可以為其每個子節點分別設置 `key`。
```html
<!-- Vue 2.x -->
<template v-for="item in list">
  <div :key="item.id">...</div>
  <span :key="item.id">...</span>
</template>
```

在 Vue 3.x 中 `key` 則應該被設置在 `<template>` 標籤上。
```html
<!-- Vue 3.x -->
<template v-for="item in list" :key="item.id">
  <div>...</div>
  <span>...</span>
</template>
```

## v-if 與 v-for 的優先級對比

### 1. 2.x 語法

2.x 版本中在一個元素上同時使用 `v-if` 和 `v-for` 時，`v-for` 會優先作用。

### 2. 3.x 語法

3.x 版本中，則剛好相反，`v-if` 總是優先於 `v-for` 生效。

不過本來就不建議同時使用這兩個指令，因此沒啥影響。

## v-bind 合併行為

`v-bind` 的綁定順序會影響渲染結果。

### 1. 2.x 語法

在 2.x 版本中，如果一個元素同時定義了 `v-bind="object"` 語法和相同的 property，那麼這個單獨的 property 一定會覆蓋 `object` 中的綁定。
 
```html
<!-- Vue 2.x -->
<!-- template -->
<div id="red" v-bind="{ id: 'blue' }"></div>
<!-- result -->
<div id="red"></div>
```

### 2. 3.x 語法

在 3.x 版本中，宣告綁定的順序會決定如何合併：
```html
<!-- Vue 3.x -->
<!-- template -->
<div id="red" v-bind="{ id: 'blue' }"></div>
<!-- result -->
<div id="blue"></div>

<!-- template -->
<div v-bind="{ id: 'blue' }" id="red"></div>
<!-- result -->
<div id="red"></div>
```


## 移除 v-on.native 修飾符號

### 1. 2.x 語法

想要在子元件的根元素上監聽原生 DOM 事件可以使用 `v-on` 指令的 `.native` 修飾符號，否則預設情況下只有通過 `this.$emit` 觸發。

```html
<!-- Vue 2.x -->
<my-component @click.native="clickHandler"></my-component>
```
```javascript
// Vue 2.x
Vue.component('MyComponent', {
  template: `
    <button>click</button>
  `,
});

const vm = new Vue({
  el: '#app',
  methods: {
    clickHandler() {
      alert('onclick');
    },
  },
});
```
**CodePen Demo：**[Vue 2.x - native 修飾符號](https://codepen.io/CHUPAIWANG/pen/rNWqbro)

### 2. 3.x 語法

在 3.x 版本中，已移除 `v-on` 指令的 `.native` 修飾符號。

因為 3.x 版本新增了 `emits` 選項，只有在 `emits` 選項中定義的事件會做為自訂義事件，而未定義的事件則會作為原生事件添加到子元件的根元素中。

```html
<!-- Vue 2.x -->
<my-component v-on:click="clickHandler"></my-component>
```
```javascript
// Vue 3.x
const app = Vue.createApp({
  methods: {
    clickHandler() {
      alert('onclick');
    },
  },
});

app.component('MyComponent', {
  template: `
    <button>click</button>
  `,
});

app.mount('#app');
```
**CodePen Demo：**[Vue 3.x - 原生事件](https://codepen.io/CHUPAIWANG/pen/RwoeXVP)


## v-for 中的 Ref 陣列

`v-for` 中的 `ref` 不再註冊 ref 陣列。

### 1. ref

`ref` 可以用來取得 DOM 元素，也就是將元素註冊在 `$refs` 物件。詳細可以參考 **[Vue2.x API ref](https://cn.vuejs.org/v2/api/#ref)**。

若是在使用 `v-for` 指令元素上使用 `ref`，將會建立包含所有元素的陣列。但當使用巢狀 `v-for` 時，`$refs` 的結構就會變得相當不直覺，元素陣列是分開的：
```htmlmixed
<!-- Vue 2.x -->
<div v-for="x of 3" ref="xDoms">
  <div v-for="y of 3" ref="yDoms">{{ x * y }}</div>
</div>
```
![](https://i.imgur.com/8Ym4R6h.png)

**CodePen Demo：**[Vue2.x - 巢狀 ref](https://codepen.io/CHUPAIWANG/pen/vYyQKEj)


### 2. 3.x 語法

因此 Vue 3.x 將不再自動建立陣列，需要搭配 `v-bind` 指令將 `ref` 綁定到一個更靈活的函式上。
```htmlmixed
<!-- Vue 3.x -->
<div v-for="n of 10" :ref="setDoms">n</div>
```
```javascript
// Vue 3.x
data() {
  return {
    doms: [],
  };
},
methods: {
  setDoms(el) {
    if (el) {
      this.doms.push(el);
    }
  },
},
mounted() {
  console.log(this.doms);
},
```
我們可以透過該函式將取得的元素迭代放置在一個陣列或物件中。

**CodePen Demo：**[Vue3.x - ref](https://codepen.io/CHUPAIWANG/pen/KKNrMmd)

如果 v-for 的元素是動態的，要確保在每次更新之前重置容器：
```javascript
beforeUpdate() {
  this.doms = [];
}
```
**CodePen Demo：**[Vue3.x - ref 更新](https://codepen.io/CHUPAIWANG/pen/YzNaarO)

