---
title: "CompositionEvent"
date: 2020-09-27
description: "CompositionEvent 處理輸入法狀態判斷。"
keywords: ["CompositionEvent", "竹白"]
tags: ["CompositionEvent", "w3HexSchool"]
categories: ["w3HexSchool六角鼠年鐵人賽"]
image: images/covers/2009/compositionEvent.png
draft: false
libraries:
- katex
---

這週是六角鼠年鐵人賽第三十四週。

<!--more-->

{{< featuredImage >}}

## 前言

在 Vue 文件的 **[表單輸入綁定](https://cn.vuejs.org/v2/guide/forms.html)** 中有提到這一段：
> 對於需要使用輸入法 (如中文、日文、韓文等) 的語言，你會發現 `v-model` 不會在輸入法組合文字過程中得到更新。如果你也想處理這個過程，請使用 `input` 事件。

<iframe height="265" style="width: 100%;" scrolling="no" title="v-model" src="https://codepen.io/CHUPAIWANG/embed/gOryYbE?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/gOryYbE'>v-model</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

這是因為 Vue 有使用到 CompositionEvent，你可以在 `v-model` 的 **[原始碼](https://github.com/vuejs/vue/blob/v2.6.10/src/platforms/web/runtime/directives/model.js)** 中看到。

另外，在 [Element UI](https://element.eleme.io/#/zh-CN) 的 Input 元件 **[原始碼](https://github.com/ElemeFE/element/blob/dev/packages/input/src/input.vue)**，你可以看到它的身影


## CompositionEvent 簡介

[CompositionEvent](https://developer.mozilla.org/en-US/docs/Web/API/CompositionEvent) 複合事件，是用來處理[輸入法編輯器](https://zh.wikipedia.org/wiki/%E8%BE%93%E5%85%A5%E6%B3%95)（Input Method Editor, IME）的事件，從 DOM Level 3 之後才新增的。

在使用輸入法編輯器時，會根據輸入狀態觸發下事件：
- [`compositonstart`](https://developer.mozilla.org/en-US/docs/Web/API/Element/compositionstart_event)：開始拼字時觸發。
- [`compositionupdate`](https://developer.mozilla.org/en-US/docs/Web/API/Element/compositionupdate_event)：選字時觸發。
- [`compositionend`](https://developer.mozilla.org/en-US/docs/Web/API/Element/compositionend_event)：拼字完成時觸發。

CompositionEvent 會在 `keydown` 事件之後被觸發。

假設我們不使用 `v-model` 來雙向綁定輸入框：
```html
<input type="text" :value="text" @input="inputHandler">
<p>內容： {{ text }}</p>
<p>字數： {{ text.length }}</p>
```
```javascript
data: {
  text: '',
},
methods: {
  inputHandler(event) {
    this.text = event.target.value;
  },
}
```

你就會發現，當你使用注音選字時，會觸發更新：
<iframe height="265" style="width: 100%;" scrolling="no" title="DEMO" src="https://codepen.io/CHUPAIWANG/embed/qBZvezp?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/qBZvezp'>DEMO</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

新增一個 `isComposing` 狀態來判斷輸入法是否在使用中：
```javascript
data: {
  text: '',
  isComposing: false,
}
```

接著，我們要監聽 `compositonstart` 和 `compositionend` 事件：
```html
  <input 
    type="text"
    :value="text"
    @input="inputHandler"
    @compositionstart="isComposing = true"
    @compositionend="handleCompositionEnd"
  >
  <p>內容： {{ text }}</p>
  <p>字數： {{ text.length }}</p>
```
- 當 `compositionstart` 觸發，`isComposing` 設為 `true`；
- 當 `compositionend` 觸發，`isComposing` 設為 `false` 並呼叫 `inputHandler`。

```javascript
methods: {
  inputHandler(event) {
    if (this.isComposing) { return; }
    this.text = event.target.value;
  },
  handleCompositionEnd(event) {
    if (this.isComposing) {
      this.isComposing = false;
      this.inputHandler(event);
    }
  }
}
```
<iframe height="265" style="width: 100%;" scrolling="no" title="CompositionEvent DEMO" src="https://codepen.io/CHUPAIWANG/embed/wvGZwKw?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/wvGZwKw'>CompositionEvent DEMO</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## isComposing 事件屬性

另外，KeyboardEvent 和 InputEvent 有 [`KeyboardEvent.isComposing`](https://developer.mozilla.org/zh-CN/docs/Web/API/KeyboardEvent/isComposing)、 [`InputEvent.isComposing`](https://developer.mozilla.org/zh-CN/docs/Web/API/InputEvent/isComposing)。`isComposing` 屬性在 `compositonstart` 和 `compositionend` 之間其值為 `true`。

但是 Safari（WebKit）瀏覽器不支援。

## 應用

使用 CompositionEvent 來判斷輸入法狀態，可以有效避免 input 監聽事件在拼字時一直觸發。

也可以在用在使用 Enter 鍵送出資料時的優化。舉例來說，在某些瀏覽器下，如果使用 `@keyup.enter="submit"` 送出資料，當我們在選字時所按下 Enter 鍵，就會觸發事件：

因此我們可以加上輸入法狀態判斷避免誤觸：
```html
<input type="text"
  v-model="text"
  @keyup.enter="submit"
  @compositionstart="isComposing = true"
  @compositionend="isComposing = false"
>
<ul>
  <li v-for="item of list">{{ item }}</li>
</ul>
```
```javascript
data: {
  text: '',
  list: [],
  isComposing: false,
},
methods: {
  submit(event) {
    if (this.isComposing) { return }
    this.list.push(this.text);
  }
}
```
[CodePen Demo](https://codepen.io/CHUPAIWANG/pen/OJNGLaM)

等同：
```html
<input type="text"
  v-model="text"
  @keyup.enter="submit"
>
<ul>
  <li v-for="item of list">{{ item }}</li>
</ul>
```
```javascript
data: {
  text: '',
  list: [],
},
methods: {
  submit(event) {
    if (event.isComposing) { return }
    this.list.push(this.text);
  }
}
```
但是 `isComposing` 事件屬性 Safari（WebKit）瀏覽器不支援。