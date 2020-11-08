---
title: "Vue元件 - Slot 插槽"
date: 2020-10-22
description: "Vue 元件 Slot 插槽。"
keywords: ["Vue","Vue元件","竹白"]
tags: ["Vue", "Vue元件"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/component/slot.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## Slot 插槽

元件就是用來重複使用的，但不可能每次使用都用同樣的模板。而 **`<slot>` 插槽** 可以使我們的元件更佳彈性，能從外層將模板放置指定位置上。

## 匿名插槽

首先，這是沒有 `<slot>` 的元件：
```javascript
Vue.component('c-demo', {
  template: `
    <div>
      <p>Demo</p>
    </div>
  `,
});
```

因此如果我們直接在元件 `tag` 放置內容是沒有意義的。
```html
<c-demo><p>123</p></c-demo>

<!-- 渲染 -->
<div><p>Demo</p></div>
```
想要新增的內容並不會被渲染出來。

這裡要使用插槽，需要在元件模板加上 `<slot>`：
```javascript
Vue.component('c-demo', {
  template: `
    <div>
      <p>Demo</p>
      <slot></slot>
    </div>
  `,
});
```
```html
<c-demo><p>123</p></c-demo>

<!-- 渲染 -->
<div>
  <p>Demo</p>
  <p>123</p>
</div>
```
原本 `<slot>` 的位置就會被替換成添加的元素。

**CodePen Demo：**[Vue 2.x - 匿名插槽](https://codepen.io/CHUPAIWANG/pen/xxVmQdV)

如果你設置多個 `<slot>`，內容就會重複：
```javascript
Vue.component('c-demo', {
  template: `
    <div>
      <p>Demo</p>
      <slot></slot>
      <slot></slot>
      <slot></slot>
    </div>
  `,
});
```
```html
<c-demo><p>123</p></c-demo>

<!-- 渲染 -->
<div>
  <p>Demo</p>
  <p>123</p>
  <p>123</p>
  <p>123</p>
</div>
```

另外，`<slot>` 內可以加上預設元素：
```javascript
Vue.component('c-demo', {
  template: `
    <div>
      <p>Demo</p>
      <slot>
        <p>預設內容</p>
      </slot>
    </div>
  `,
});
```
如果沒有添加元素，就會顯示預設元素。

## 具名插槽

{{< notice warning >}}
`slot` 特性語法在 2.6.0 起被廢棄，由 [`v-slot`](https://cn.vuejs.org/v2/api/#v-slot) 指令取代，有興趣可以參考 **[官方文件](https://cn.vuejs.org/v2/guide/components-slots.html#废弃了的语法)**。
{{< /notice >}}

### 1. 基本範例

如果我們有多個插槽的需求，就需要為每個 `<slot>` 加上 `name` 特性，也就是具名插槽：
```javascript
Vue.component('c-demo', {
  template: `
    <div>
      <h1>
        <slot name="title"></slot>
      </h1>
      <main>
        <slot name="content"></slot>
      </main>
      <footer>
        <slot name="footer"></slot>
      </footer>
    </div>
  `,
});
```

在外層插入元素時，必須在 `<template>` 元素上使用 [`v-slot`](https://cn.vuejs.org/v2/api/#v-slot) 指令，指定元素插入的位置：
```html
<c-demo>
  <template v-slot:title>
    Title
  </template>
  <template v-slot:content>
    <p>123456</p>
  </template>
  <template v-slot:footer>
    end
  </template>
</c-demo>

<!-- 渲染 -->
<div>
  <h1>Title</h1>
  <main>
    <p>123456</p>
  </main>
  <footer>end</footer>
</div>
```
**CodePen Demo：**[Vue 2.x - 具名插槽](https://codepen.io/CHUPAIWANG/pen/XWdooOZ)

### 2. 混用

如果使用具名插槽又使用匿名插槽：
```javascript
Vue.component('c-demo', {
  template: `
    <div>
      <slot></slot>
      <slot name="content"></slot>
    </div>
  `,
});
```
```html
<c-demo>
  <p>111</p>
  <template v-slot:content>
    <p>222</p>
  </template>
  <p>333</p>
  <template>
    <p>444</p>
  </template>
  <p>555</p>
</c-demo>

<!-- 渲染 -->
<div>
  <p>111</p>
  <p>333</p>
  <p>444</p>
  <p>555</p>
  <p>222</p>
</div>
```
任何沒有被包裹在帶有 `v-slot` 的 `<template>` 的內容都會被放進匿名插槽中。

**CodePen Demo：**[Vue 2.x - Slot 插槽測試](https://codepen.io/CHUPAIWANG/pen/OJNremv)

匿名插槽預設 `name` 特性名稱為 `default`，因此如果我們使用 `<template v-slot:default>` 時，這裡的內容將被放置在匿名插槽中，而且任何沒有被包裹在帶有 `v-slot` 的 `<template>` 的內容都將無效：
```html
<c-demo>
  <p>1111</p>
  <template v-slot:default>
    <p>222</p>
  </template>
  <template>
    <p>333</p>
  </template>
</c-demo>

<!-- 渲染 -->
<p>222</p>
```

**CodePen Demo：**[Vue 2.x - Slot 插槽測試2](https://codepen.io/CHUPAIWANG/pen/VwaqJdz)

### 3. 縮寫

另外 `v-slot` 指令也有縮寫 `#`：
```html
<template v-slot:#title></template>

<!-- 等同 -->
<template #title></template>
```
與其他縮寫一樣，只有在有參數時，才能使用。


## 作用域插槽

元件 `tag` 位於父層，如果希望插槽訪問元件的資料，你可以這樣做。

在設置插槽時，可以綁定 `插槽 prop`：
```javascript
Vue.component('c-demo', {
  template: `
    <div>
      <slot :obj="abc"></slot>
    </div>
  `,
  data() {
    return {
      abc: {
        a: 'aaa',
        b: 'bbb',
      },
    };
  },
});
```
`obj` 為我們定義的 `插槽 prop`，`abc` 為綁定的資料。

接下來，我們就能在父層使用 `v-slot` 指令，可以取得一個物件（可以任意命名），這個物件會包含 `<solt>` 上所有綁定的 `插槽 prop`：
```html
<c-demo >
  <template v-slot="myData">
    {{ myData.obj.a }}
  </template>
</c-demo>
```
**CodePen Demo：**[Vue 2.x - 作用域插槽](https://codepen.io/CHUPAIWANG/pen/PoNXVZZ)

{{< notice warning >}}
但要注意，上面 `v-slot="myData"` 屬於匿名插槽的縮寫，當你有使用其它具名插槽時，不能與匿名插槽的縮寫混用，會導致作用域不明確，應該要正確使用原本應該要有的語法 `v-slot:default="myData"`。
{{< /notice >}}

另外，我們還可以解構插槽，例如將上面程式碼改成這樣：
```html
<c-demo >
  <template v-slot="{ obj }">
    {{ obj.a }}
  </template>
</c-demo>
```

甚至可以定義預設值：
```html
<c-demo >
  <template v-slot="{ obj = { a: 'a'} }">
    {{ obj.a }}
  </template>
</c-demo>
```