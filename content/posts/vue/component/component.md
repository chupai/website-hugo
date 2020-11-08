---
title: "Vue元件 - 基礎"
date: 2020-10-19
description: "Vue 元件基礎。"
keywords: ["Vue","Vue元件","竹白"]
tags: ["Vue", "Vue元件"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/component/component.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## 元件

元件的概念就是將整個頁面拆分成一個一個的小區塊，然後像積木一樣把頁面組合起來。

![](https://i.imgur.com/OuZZAzJ.png)

拆分成元件的優點：
- 只需要維護自己依賴的 CSS、JS；
- 元件可以進行任意次數的復用；
- 獨立的 `data`、`computed`、`watch`、`methods` 等等。

注意事項：
- `data` 必須使用 `function` 函式；
- 需註冊才能使用。

元件中 `data` 必須是函式的原因是，元件是可以重複利用的，在正常的情況下，它會使用不同的資料。使用函式建構資料，在建立新的元件時，都會回傳一個新的物件。


## 註冊

註冊元件的方式有兩種：
1. 全域（Global）註冊
2. 區域（Local）註冊

不論全域註冊或是區域註冊，都必須在 Vue 實體之前完成。

### 1. 全域註冊

如果有元件共用的需求，我們會使用 [`Vue.component()`](https://cn.vuejs.org/v2/api/#Vue-component) 來全域註冊一個元件。

語法：
```javascript
Vue.component(id, options);
```
- `id`：元件名稱
- `options`：選項物件

舉例來說，定義一個名為 `c-layout` 的元件：
```javascript
Vue.component('c-layout', {
  // 注意，元件必須使用 function
  data() {
    return {
      text: '全域註冊',
    };
  },
  template: '<div>{{ text }}</div>',
});
```

全域註冊後，就可以在任何 Vue 實體中使用：
```html
<div id="app">
  <c-layout></c-layout>
</div>
```
```javascript
const vm = new Vue({
  el: '#app',
});
```
<iframe height="200" style="width: 100%;" scrolling="no" title="Vue 2.x - 元件全域註冊" src="https://codepen.io/CHUPAIWANG/embed/rNeQaQr?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/rNeQaQr'>Vue 2.x - 元件全域註冊</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 2. 區域註冊

使用全域註冊的缺點是，不論是否有使用到都會載入，因此如果載入大量用不到的元件，對網頁整體來說較不佳，會拖慢載入速度。

因此可以使用 [`components`](https://cn.vuejs.org/v2/api/#components) 選項來區域註冊元件，這個元件只能註冊的 Vue 實體上使用。

定義元件的選項物件：
```javascript
const Clayout = {
  data() {
    return {
      text: '區域註冊',
    }
  },
  template: '<div>{{ text }}</div>',
};
```

在 `component` 選項中載入：
```javascript
const vm = new Vue({
  el: '#app',
  components: {
    'c-layout': Clayout,
  },
});
```
<iframe height="200" style="width: 100%;" scrolling="no" title="Vue 2.x - 元件區域註冊" src="https://codepen.io/CHUPAIWANG/embed/abNQzxM?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/abNQzxM'>Vue 2.x - 元件區域註冊</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


## 命名規則

### 1. 命名限制

元件的命名限制：
1. 不使用非法的 `tag` 字元；
2. 不與 HTML 元素或 SVG 元素重名；
3. 不使用 Vue 保留的 slot 和 component；
4. 避免與 Vue 內建的 KeepAlive、Transition、TransitionGroup 三個元件重名。

### 2. kebab-case

命名元件時，可以使用 `kebab-case` 或 `camelCase`：
```javascript
// kebab-case
Vue.component('text-component', {...});
```
```javascript
// camelCase
Vue.component('textComponent', {...});
```

但是瀏覽器在解析 HTML tag 時，無大小寫之分，因此在使用元件時，必須要改成 `kebab-case`：
```html
<text-component></text-component>
```
因此要注意，統一命名寫法別混用，避免重複命名元件。

:::success
若是在 `.vue` 單一元件檔的 `<template>` 中使用元件，HTML tag 則無大小寫限制。
:::

### 3. 風格指南

在 **[官方的風格指南](https://cn.vuejs.org/v2/style-guide/#优先级-B-的规则：强烈推荐-增强可读性)** 有許多關於元件命名的建議。


## Templates

### 1. 建立元件模板

定義元件的 Templates 有兩種作法：
- String Template
- DOM Template

上面的範例就是使用 String Template，直接利用字串定義元件的 `template` 選項。但是這個做法的缺點很明顯，假如介面非常複雜，會讓程式變的難以閱讀及維護。

因此可以改用 DOM Template，主要是透過 `<script type="text/x-template">` 來建立。

```html
<div id="app">
 <c-layout></c-layout>
</div>

<script type="text/x-template" id="cLayoutTemplate">
  <div>{{text}}</div>
</script>
```
```javascript
const Clayout = {
  data() {
    return {
      text: 'DOM Template',
    }
  },
  template: '#componentLayoutTemplate',
};

const vm = new Vue({
  el: '#app',
  components: {
    'c-layout': Clayout,
  },
});
```

<iframe height="200" style="width: 100%;" scrolling="no" title="Vue 2.x - 建立元件模板" src="https://codepen.io/CHUPAIWANG/embed/JjXeboQ?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/JjXeboQ'>Vue 2.x - 建立元件模板</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


### 2. 解析 DOM 模板的注意事項

在 HTML 中，`<ul>`、`<ol>`、`<table>`、`<select>` 等 `tag` 底下不能隨便塞入其他 `tag`。格式不合法的話，不同瀏覽器會有不同的解析結果，有的會直接刪除或是移到外層。

請考慮以下程式碼：
```javascript
Vue.component('c-layout', {
  template: `
    <tr>
      <td>TEST</td>
    </tr>
  `,
});
```
```html
<div id="app">
  <table>
    <c-layout></c-layout>
  </table>
</div>

<!-- 渲染成 -->
<div id="app">
  <tr>
    <td>TEST</td>
  </tr>
  <table></table>
</div>
```
因為我們在 `<table>` 內放了 `<c-layout>`，被瀏覽器認為式非法 `tag`，所以被移到外層。

因此 Vue 提供了 `is` 特性的語法，可用來引入元件。

將上述程式碼改成：
```html
<div id="app">
  <table>
    <tr is="c-layout"></tr>
  </table>
</div>

<!-- 渲染成 -->
<div id="app">
  <table>
    <tbody>
      <tr>
        <td>TEST</td>
      </tr>
    </tbody>
  </table>
</div>
```

但是如果我們是在以下 Template 來源中使用元件，這條限制是不存在的：
- 字串
- 單一元件檔`.vue`
- `<script type="text/x-template">`

舉例來說：
```javascript
Vue.component('c-layout', {
  template: `
    <table>
      <c-layout2></c-layout2>
    </table>
  `,
});

Vue.component('c-layout2', {
  template: `
    <tr>
      <td>TEST</td>
    </tr>
  `,
});
```
```html
<div id="app">
  <c-layout></c-layout>
</div>

<!-- 渲染成 -->
<div id="app">
  <table>
    <tr>
      <td>TEST</td>
    </tr>
  </table>
</div>
```


### 3. 外層根元素

Template 一定要有一個外層的根元素（Root element），不然沒辦法知道這個元件的邊界在哪。

舉例來說，不能將寫成這樣：
```html
<!-- 錯誤示範1 -->
<p>text</p>
<span>123</span>

<!-- 錯誤示範2 -->
<template>
  <p>text</p>
  <span>123</span>
</template>
```
以上皆會拋出錯誤。

必須要有一個根元素將它包起來：
```html
<div>
  <p>text</p>
  <span>123</span>
</div>
```

## 動態切換

我們可以透過 [`<component>`](https://cn.vuejs.org/v2/api/#component) 和 `is` 特性，動態切換不同的元件。

這裡有三個不同的元件：
```javascript
Vue.component('c-aaa', {
  template: '<p>component A</p>',
});
Vue.component('c-bbb', {
  template: '<p>component B</p>',
});
Vue.component('c-ccc', {
  template: '<p>component C</p>',
});

const vm = new Vue({
  el: '#app',
  data: {
    current: 'c-aaa',
  },
});
```

使用 `v-bind:is` 來動態切換元件：
```html
<div id="app">
  <button @click="current = 'c-aaa'">A</button>
  <button @click="current = 'c-bbb'">B</button>
  <button @click="current = 'c-ccc'">C</button>
  <component> v-bind:is="current"></component>
</div>
```

## 維持元件生命週期

切換元件時，可能會遇到一個問題，Vue 會直接把 DOM 上的元件消滅再重渲染一份。如果你有保留狀態的需求，可以在外層包一層 Vue 內建元件 [`<keep-alive>`](https://cn.vuejs.org/v2/api/#keep-alive)。

請考慮以下程式碼：
```javascript
Vue.component('c-counter', {
  data() {
    return {
      count: 0,
    };
  },
  template: `
  <div>
    <button @click="count += 1">+1</button>
    <p>{{ count }}</p>
  </div>`,
});

const vm = new Vue({
  el: '#app',
  data: {
    show: false,
  },
});
```
```html
<div id="app">
  <button @click="show = !show">show Counter</button>
  <c-counter v-if="show"></counter>
</div>
```
`v-if` 會卸載 DOM 元素，因此如果我們隱藏元件後再打開，計數又會重 0 開始。

因此我們可以用 `<keep-alive>` 將元件包起來：
```html
<keep-alive>
  <c-counter v-if="show"></counter>
</keep-alive>
```
<iframe height="265" style="width: 100%;" scrolling="no" title="Vue 2.x - 維持元件生命週期" src="https://codepen.io/CHUPAIWANG/embed/YzqRpEG?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/YzqRpEG'>Vue 2.x - 維持元件生命週期</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>