---
title: "其他小改變"
date: 2021-03-15
description: "Vue2.x 與 Vue3.x 差異 - 其他小改變"
keywords: ["Vue3", "Vue2.x 與 Vue3.x 差異"]
tags: ["Vue3", "Vue2.x 與 Vue3.x 差異"]
series: ["Vue3 筆記"]
image: images/covers/2103/other.png
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

## 生命週期

- `destroyed` 生命週期選項被重命名為 [`unmounted`](https://v3.cn.vuejs.org/api/options-lifecycle-hooks.html#unmounted)；
- `beforeDestroy` 生命週期選項被重命名為 [`beforeUnmount`](https://v3.cn.vuejs.org/api/options-lifecycle-hooks.html#beforeunmount)。

另外，[`$destroy`](https://cn.vuejs.org/v2/api/#vm-destroy) 實體方法已從 Vue 3.0 中移除。

## 在 prop 的預設函式中訪問 this

Vue 3.x 的 prop 預設值的工廠函式不再有訪問 `this` 的權限。

替代方案：
- 把元件接收到的原始 prop 作為參數傳遞給預設函式；
- [`inject`](https://v3.cn.vuejs.org/guide/composition-api-provide-inject.html) API 可以在預設函式中使用。

```javascript
// Vue 3.x

import { inject } from 'vue';

export default {
  props: {
    theme: {
      default(props) {
        // props 是傳遞給元件的原始值。
        // 在任何型別/預設強制轉換之前
        // 也可以使用 `inject` 來訪問注入的 property
        return inject('theme', 'default-theme');
      },
    },
  },
};
```


## 自定義指令

Vue 3.x 對自定義指令的生命週期鉤子重新命名，與元件生命週期保持一致。

### 1. 2.x 語法

鉤子函式：
```javascript
// Vue 2.x
const MyDirective = {
  bind(el, binding, vnode) {
    // 指令第一次綁定到元素時呼叫
  },
  inserted(el, binding, vnode) {
    // 被綁定元素插入父節點時呼叫
  },
  update(el, binding, vnode, oldVnode) {
    // 所在元件的 VNode 更新時呼叫
  },
  componentUpdated(el, binding, vnode, oldVnode) {
    // 指令所在元件的 VNode 及其子 VNode 全部更新後呼叫
  },
  unbind(el, binding, vnode) {
    // 指令與元素解除綁定時呼叫
  },
});
```

### 2. 3.x 語法

```javascript
// Vue 3.x
const MyDirective = {
  created(el, binding, vnode) {
    // Vue 3.x 新增
    // 在綁定元素的 attribute 或事件監聽器被應用之前呼叫
  },
  beforeMount(el, binding, vnode) {
    // 替代 bind()
    // 當指令第一次綁定到元素並且在掛載父元件之前呼叫
  },
  mounted(el, binding, vnode) {
    // inserted
    // 綁定元素的父元件被掛載時呼叫
  },
  beforeUpdate(el, binding, vnode, prevNode) {
    // Vue 3.x 新增 
    // 在包含元件的 VNode 更新之前呼叫
  },
  updated(el, binding, vnode, prevNode) {
    // 替代 update(), componentUpdated() 
    // 在包含元件的 VNode 及其子元件的 VNode 更新之後呼叫
  },
  beforeUnmount(el, binding, vnode) {
    // Vue 3.x 新增 
    // 在綁定元素的父元件卸載之前呼叫
  },
  unmounted(el, binding, vnode) {
    // 替代 unmounted()
    // 卸載綁定元素的父元件時呼叫
  },
};
```

### 3. 鉤子函式參數

- 綁定元素的實體 `instance` 從 Vue 2.x 的 `vnode` 移到了 `binding` 中；
- Vue 3.x 移除 `binding` 中的 `name`、`expression`；
- Vue 3.x 的 `binding` 新增了 `dir`，為一個物件，註冊指令時的物件選項。
- Vue 2.x 的 `oldVnode` 改成了 `prevNnode`。

## Data 選項

`data` 選項不再接收純 `object` 形式，而需要使用 `function` 型式。

### 1. 2.x 語法

在 Vue 2.x 時，根實體上的 `data` 選項可以是純 `object` 形式：
```javascript
// Vue2.x

const vm = new Vue({
  data: {},
});
```

元件的 `data` 選項則必須是 `function` 形式。

### 2. 3.x 語法只支持 function 形式

Vue 3.x 的 `data` 選項統一使用 `function` 形式。
```javascript
// Vue 3.x
const app = new Vue({
  data() {
    return {};
  },
});
```

### 3. Mixin 合併行為變更

若元件使用 mixin 或 extends，彼此的 `data` 選項會進行合併，Vue 2.x 是深拷貝合併，但 Vue 3.x 只進行 **淺拷貝合併**。

舉例來說：
```javascript
const Mixin = {
  data() {
    return {
      user: {
        name: 'Jack',
        id: 1,
      },
    };
  },
};
const CompA = {
  mixins: [Mixin],
  data() {
    return {
      user: {
        id: 2,
      },
    };
  },
};
```

Vue 2.x 生成 `$data`：
```javascript
{
  user: {
    id: 2,
    name: 'Jack',
  },
}
```

而 Vue 3.x 將會是：
```javascript
{
  user: {
    id: 2,
  },
}
```
因為 mixin 的深度合併非常隱式，這讓程式碼邏輯難以理解。

## attribute 強制行為

{{< notice info>}} 
這是一個底層的內部 API 更改，不會影響大多數開發者。以下指說明重點差異，詳細說明請參考 **[官方文件](https://v3.vuejs.org/guide/migration/attribute-coercion.html#enumerated-attributes)**。
{{< /notice >}}

- Vue 3.x 對於 `contenteditable`、`draggable`、`spellcheck` 等 enumerated attribute 不再有特殊處理，將它們視為普通的 non-boolean attribute。
- 對於 non-boolean attribute 的值，若為 `false` 將不再刪除 attribute，它會被設置成字串 `'false'`。
  - 如果要移除 attribute 則需要使用 `null` 或者 `undefined` 顯式刪除。

| 綁定值 | Vue 2.x non-boolean | Vue 2.x enumerated | Vue 3.x non-boolean | Vue 3.x enumerated |
| --- | --- | --- | --- | --- | --- | --- |
| `null` | removed | `"false"` | removed | removed | 
| `undefined` | removed | removed | removed |removed |
| `true` | `"true"` | `"true"` | `"true"` |`"true"` |
| `false` | removed | `"false"` | `"false"` | `"false"` |
| `""` | `""` | `"true"` | `""` | `""` |
| `"true"` | `"true"` | `"true"` | `"true"` | `"true"` |
| `"false"` | `"false"` | `"false"` | `"false"` |`"false"` |
| `"foo"` | `"foo"` | `"true"` | `"foo"` | `"foo"` |
| `0` | `"1"` | `"true"` | `"1"` | `"1"` |
| `1` | `"0"` | `"true"` | `"0"` | `"0"` |


## Transition

- Transition 的部分 class 名稱更改。
- `<TransitionGroup>` 不再預設渲染包裹元素。

### 1. 部分 class 名稱更改

為了更加明確易讀，Vue 3.x 對初始狀態重命名：
- `v-enter` 修改為 `v-enter-from`
- `v-leave` 修改為 `v-leave-from`

對於 `<transition>` 元件相關屬性名稱也發生了變化：
- `leave-class` 已經被重命名為 `leave-from-class`
- `enter-class` 已經被重命名為 `enter-from-class`

### 2. \<TransitionGroup>

`<transition-group>` 不再預設渲染根元素，但仍然可以用 `tag` prop 建立根元素。

Vue 2.x 中，`<transition-group>` 與自定義元件一樣，需要一個根元素，預設為 `<span>`，可以透過 `tag` prop 更改：
```html
<!-- Vue 2.x -->
<transition-group tag="ul">
  <li v-for="item in items" :key="item">
    {{ item }}
  </li>
</transition-group>
```

Vue 3.x 有了 **[片段（Fragments）](https://v3.cn.vuejs.org/guide/migration/fragments.html#%E6%A6%82%E8%A7%88)**，元件不再需要根節點，因此 `<transition-group>` 也不再預設渲染根節點。但仍然能透過 `tag` prop 添加。

## Watch on Arrays

Vue 3.x 監聽陣列現在必須開啟 `deep` 選項。

### 1. 2.x 行為

監聽的 `deep` 選項在 Vue 2.x 只用於處理物件的深度監聽，對於陣列監聽並不需要特別設置 `deep: true`。

**CodePen Demo：**[Vue 2.x - Watch on Arrays](https://codepen.io/CHUPAIWANG/pen/vYyQrVJ)

### 2. 3.x 更新

Vue 3.x 則統一監聽物件處理，包括陣列也需設置 `deep: true`。
```javascript
// Vue 3.x
watch: {
  arr: {
    deep: true,
    handler() {
      this.count += 1;
    },
  },
}
```
**CodePen Demo：**[Vue 3.x - Watch on Arrays](https://codepen.io/CHUPAIWANG/pen/oNYQMbG)


## \<template> 相關

沒有特殊指令的標記（`v-if`/`else-if`/`else`、`v-for` 或 `v-slot`）的 `<template>` 現在被視為普通元素，並將生成原生的 `<template>` 元素，而不是渲染其內部內容。