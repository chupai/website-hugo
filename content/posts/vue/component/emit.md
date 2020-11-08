---
title: "Vue元件 - 自定義事件"
date: 2020-10-21
description: "Vue 元件自定義事件。"
keywords: ["Vue","Vue元件","竹白"]
tags: ["Vue", "Vue元件"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/component/emit.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## 自定義事件

在開發元件時，有時候功能還是需要與外層做溝通，但 `prop` 是單向的。因此 Vue 實體提供了一個 **自定義事件** 的系統來解決這個問題，使用事件來回應外層資料。

### 1. 基本範例

舉個簡單的範例，我們要讓元件 `c-button` 能夠執行外層的方法。

首先設定元件會如何觸發監聽方法，在監聽方法內透過 [`$emit`](https://cn.vuejs.org/v2/api/#vm-emit) 方法傳入自定義事件名稱。
```javascript
Vue.component('c-btn', {
  methods: {
    clickHandler() {
      this.$emit('my-event');
    },
  },
  template: `<button @click="clickHandler">add</button>`,
});
```

使用 `v-on` 監聽自定義事件 `my-event`，並指定要執行事情：
```html
<div id="app">
  <p>{{ counter }}</p>
  <c-btn @my-event="addCounter"></c-btn>
</div>
```
```javascript
const vm = new Vue({
  el: '#app',
  data: {
    counter: 0,
  },
  methods: {
    addCounter() {
      this.counter += 1;
    },
  },
});
```
<iframe height="200" style="width: 100%;" scrolling="no" title="Vue 2.x - Emit" src="https://codepen.io/CHUPAIWANG/embed/JjXwoRL?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/JjXwoRL'>Vue 2.x - Emit</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 2. 使用事件拋出一個值

`$emit` 的第二個參數能夠將值拋出，這個值將會作為第一個參數傳入外層方法：
```javascript
Vue.component('c-btn', {
  methods: {
    clickHandler() {
      this.$emit('my-event', 3);
    },
  },
  template: `<button @click="clickHandler">add</button>`,
});

const vm = new Vue({
  el: '#app',
  data: {
    counter: 0,
  },
  methods: {
    addCounter(val) {
      this.counter += val;
    },
  },
});
```
<iframe height="265" style="width: 100%;" scrolling="no" title="Vue 2.x - 拋出一個值" src="https://codepen.io/CHUPAIWANG/embed/ZEWVGVP?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/ZEWVGVP'>Vue 2.x - 拋出一個值</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## 自定義事件名稱

不同於元件和 `prop`，事件名稱無法自動化的大小寫轉換。在 HTML 中使用自定義事件時，需要完全匹配名稱。

如果觸發一個 `camelCase` 風格命名的事件：
```javascript
this.$emit('myEvent');
```
監聽這個名字的 `kebab-case` 版本是不會有任何效果的：
```html
<!-- 沒有效果 -->
<c-demo @my-event="doSomething"></c-demo>
```

官方推薦始終使用 `kebab-case` 風格命名自定義事件。

## 在元件上使用 v-model

`v-model` 可以在表單元素上實現雙向綁定，也可以用在元件上。

### 1. 基本範例

請考慮以下情況，假設有一個輸入元件，我們想要實現資料雙向綁定：
```javascript
Vue.component('c-demo', {
  template: `
    <div>
      <input type="text" />
    </div>
  `,
});
```

你可能需要先用 `prop` 傳遞給元件，再使用 `$emit` 拋值給父層：
```javascript
Vue.component('c-demo', {
  props: ['inputText'],
  data() {
    return {
      text: this.inputText,
    };
  },
  methods: {
    inputHandler(event) {
      this.text = event.target.value;
      this.$emit('my-event', this.text);
    },
  },
  template: `
    <div>
      <input type="text"
        :value="text"
        @input="inputHandler"
      />
    </div>
  `,
});

const vm = new Vue({
  el: '#app',
  data: {
    text: 'hello',
  },
  methods: {
    demoHandler(value) {
      this.text = value;
    },
  },
});
```
```html
<div id="app">
  <p>{{ text }}</p>
  <c-demo :input-text="text" @my-event="demoHandler"></c-demo>
</div>
```
**CodePen Demo：**[Vue 2.x - 元件雙向綁定](https://codepen.io/CHUPAIWANG/pen/OJNrMry)

這樣子的作法非常麻煩且複雜，我們可以改用 `v-model` 雙向綁定資料：
```html
<div id="app">
  <p>{{ text }}</p>
  <c-demo v-model="text"></c-demo>
</div>
```
```javascript
Vue.component('c-demo', {
  props: ['value'],
  template: `
    <div>
      <input
        :value="value"
        @input="$emit('input', $event.target.value)"
        type="text" />
    </div>
  `,
});

const vm = new Vue({
  el: '#app',
  data: {
    text: 'hello',
  },
});
```
我們需要將 `value` 特性綁定到一個名叫 `value` 的 `prop` 上，並在事件被觸發時，將新的值透過自定義的 `input` 事件拋出。

**CodePen Demo：**[Vue 2.x - 在元件上使用 v-model](https://codepen.io/CHUPAIWANG/pen/LYNMGzJ)

### 2. model 選項

一個元件上的 `v-model` 預設會利用名為 `value` 的 `prop` 和名為 `input` 的自定義事件。

除了預設值外，我們可以使用 `model` 選項來設定 `prop` 和 `event`：
```javascript
Vue.component('c-checkbox', {
  model: {
    prop: 'checked',
    event: 'my-event',
  },
  props: {
    checked: Boolean,
  },
  template: `
    <div>
      <input 
        :checked="checked"
        @change="$emit('my-event', $event.target.checked)"
        type="checkbox" />
    </div>
  `,
});
```
**CodePen Demo：**[Vue 2.x - model 選項](https://codepen.io/CHUPAIWANG/pen/WNwLRpL)

這樣子可以有效避免不同的元件上的語意衝突。

## 綁定原生事件

### 1. 基本範例

在元件 `tag` 上使用事件綁定，全都屬於自定義事件，因此若要使用原生事件，你可能會這樣做：
```html
<c-button @my-event="clickHandler"></c-button>
```
```javascript
Vue.component('c-button', {
  template: `
    <button @click="$emit('my-event')">add</button>
  `,
});

const vm = new Vue({
  el: '#app',
  methods: {
    clickHandler() {
      // ...
    },
  },
});
```

這裡我們可以改用事件修飾符號 `.native`，這樣元件 `tag` 就能使用元生事件 `click`，會綁定在根元素上：
```html
<c-button @click.native="clickHandler"></c-button>
```
```javascript
Vue.component('c-button', {
  template: '<button>add</button>',
});

const vm = new Vue({
  el: '#app',
  methods: {
    clickHandler() {
      // ...
    },
  },
});
```
**CodePen Demo**：[Vue 2.x - 綁定原生事件](https://codepen.io/CHUPAIWANG/pen/MWyZJzw)

### 2. 根元素非監聽元素

但是，這種方式只能在用在根元素剛好是要監聽的元素。

如果我們的元件長這樣：
```javascript
Vue.component('c-button', {
  template: `
    <div>
      <button>add</button>
      <button>+1</button>
    </div>
  `,
});
```

就需要用到 Vue 提供的 [`$listeners`](https://cn.vuejs.org/v2/api/#vm-listeners) 屬性，它是一個物件，裡面包含了作用在這個元件上的所有監聽器（不含使用 `.native` 的事件）。

因此我們要先將 `inheritAttrs: false`，並配合 `v-on="$listeners"` 將所有的事件監聽器指向指定的元素。
```javascript
Vue.component('c-button', {
  inheritAttrs: false,
  template: `
    <div>
      <button v-on="$listeners">add</button>
      <button>+1</button>
    </div>
  `,
});
```
```html
<div id="app">
  <p>{{ counter }}</p>
  <c-button @click="clickHandler"></c-button>
</div>
```
**CodePen Demo**：[Vue 2.x - 根元素非監聽元素](https://codepen.io/CHUPAIWANG/pen/RwaEppv)

{{< notice warning >}}
`v-on="$listeners"` 這裡不能使用縮寫 `@`，，因為沒綁定事件。
{{< /notice >}}

## sync 修飾符號

若希望達到父子元件之間的 `prop` 進行雙向綁定必須自行實作兩個步驟：
- 從父元件利用 `prop` 傳值到子元件；
- 父元件監聽自訂的事件。當子元件發生變更時，使用 `$emit('event', value)` 將變動通知父元件，並於父元件中自行更改值。

```html
<div id="app">
  <p>{{ counter }}</p>
  <c-button
    :counter="counter"
    @counter-update="updateCounter"
  ></c-button>
</div>
```
```javascript
Vue.component('CButton', {
  props: ['counter'],
  methods: {
    clickHandler() {
      const newCounter = this.counter + 1;
      this.$emit('counter-update', newCounter);
    },
  },
  template: '<button @click="clickHandler">add</button>',
});

const vm = new Vue({
  el: '#app',
  data: {
    counter: 0,
  },
  methods: {
    updateCounter(val) {
      this.counter = val;
    },
  },
});
```
**CodePen Demo：**[Vue 2.x - 雙向綁定](https://codepen.io/CHUPAIWANG/pen/ZEWVypg)

Vue 替這種模式提供一個方便的縮寫，即 `.sync` 修飾符號：
```html
<div id="app">
  <p>{{ counter }}</p>
  <c-button :counter.sync="counter"></c-button>
</div>
```
```javascript
Vue.component('CButton', {
  props: ['counter'],
  methods: {
    clickHandler() {
      const newCounter = this.counter + 1;
      this.$emit('update:counter', newCounter);
    },
  },
  template: '<button @click="clickHandler">add</button>',
});

const vm = new Vue({
  el: '#app',
  data: {
    counter: 0,
  },
});
```
使用 `update:myPropName` 取代外層的更新事件，省略非常多程式碼。

**CodePen Demo：**[Vue 2.x - .sync 修飾符號](https://codepen.io/CHUPAIWANG/pen/jOqXwBJ)

{{< notice error >}}
但這裡要小心，`.sync` 不能和表達式一起使用，因為你是要雙向綁定資料。
{{< /notice >}}

另外，如果使用一個物件同時設置多個 `prop` 時，並且需要雙向綁定，也可以使用 `.sync`。

**CodePen Demo：**[Vue 2.x - .sync 修飾符號 - 物件](https://codepen.io/CHUPAIWANG/pen/JjXwJQy)

如果只有一個 `prop` 需要雙向綁定，可以選用 `v-model`，若需要雙向綁定多個 `prop` 才使用 `.sync`。

