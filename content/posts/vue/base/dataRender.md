---
title: "資料渲染"
date: 2020-10-08
description: "Vue 作為資料驅動畫面的框架，首先必須要了解如何將資料在畫面中展示出來。"
keywords: ["Vue","資料渲染","竹白"]
tags: ["Vue"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/base/dataRender.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## 資料綁定

Vue 作為資料驅動畫面的框架，首先必須要了解如何將資料在畫面中展示出來。

傳統 Web 是透過後端的樣板引擎來進行資料與畫面的渲染，會導致前後端語法交雜在一起（前端 HTML 文件包含了後端樣板引擎的語法），而且渲染後要再次修改畫面，就只能透過獲取 DOM 的方法進行修改，來保持畫面與資料的一致。

Vue 的核心是一個響應式的資料綁定系統，建立綁定後，DOM 將和資料保持同步，這樣就不需要手動維護 DOM，使程式碼能夠更加簡潔易懂，提升效率。

## 文字 Text

最基礎的形式就是文字，使用雙大括號 `{{}}`（Mustache 語法）。

語法範例：
```javascript
data: {
  message: 'Hello Vue.js!',
}
```
```html
<p>{{ message }}</p>

<!-- HTML 將被渲染成 -->
<p>Hello Vue.js!</p>
```
Mustache 語法中的變數會去 Vue 實體中找。

文字除了使用 Mustache 語法，[`v-text`](https://cn.vuejs.org/v2/api/index.html#v-text) 指令也可以渲染文字：
```html
<p v-text="message"></p>

<!-- 等同 -->
<p>{{ message }}</p>
```
差異在於，使用 `v-text` 會取代原本元素的內容，而使用 Mustache 語法可以合併內容：
```html
<p>message：{{ message }}</p>

<!-- 等同 -->
<p>message：Hello Vue.js!</p>
```

若同時使用，Mustache 語法會被 `v-text` 取代。

## v-once

如果元素上所綁定的資料只需要渲染一次，之後不會在更新它，可以使用 [`v-once`](https://cn.vuejs.org/v2/api/index.html#v-once) 指令，助於優化更新性能：
```html
<p v-once>Message: {{ message }}</p>
```
**CodePen Demo：**[Vue 2.x - v-once](https://codepen.io/CHUPAIWANG/pen/vYGbvLR)

可用於低開銷的靜態元件上。

## HTML 標籤

如果要將資料渲染成 HTML 標籤，就需要使用 [`v-html`](https://cn.vuejs.org/v2/api/index.html#v-html) 指令。

```html
<p v-html="'<span>123</span>'"></p>

<!-- HTML 將被渲染成 -->
<p><span>123</span></p>
```

使用 `v-html` 的元素，若有內容會被取代掉：
```html
<p v-html="'<span>123</span>'">某段內容</p>

<!-- HTML 將被渲染成 -->
<p><span>123</span></p>
```

不過 `v-html` 可以不使用，盡可能不使用。


## Attributes

Mustache 語法不能作用在 HTML atributes 上，這時可以使用 `v-bind` 指令：
```html
<div v-bind:id="dynamicId"></div>
```
更詳細的說明會在特性綁定介紹。

## JavaScript Expressions

Vue.js 提供了完全的 JavaScript Expressions（表達式）支持。

舉例來說：
```html
<p>{{ 1 + 1 }}</p>
<p>{{ 'Hello'.split('').reverse().join('') }}</p>

<!-- HTML 將被渲染成 -->
<p>{{ 2 }}</p>
<p>olleH</p>
```

但要注意的是，只支持表達式，並不支持語句，舉例來說：
```html
<p>{{ var a = 1 }}</p>

<p>{{ if (ok) { return message } }}</p>
```
以上皆會拋出錯誤。條件判斷，可以改用三元運算子。

另外還有一點，除了某些 **[白名單中](https://github.com/vuejs/vue/blob/v2.6.10/src/core/instance/proxy.js#L9)** 中的全域變數外，不能使用其他自定義的全域變數。

也就是說，我們能直接使用 `Date`：
```html
<p>版權所有 {{ '© ' + new Date().getFullYear() }}</p>

<!-- HTML 將被渲染成 -->
<p>版權所有 © 2020</p>
```
是因為 `Date` 物件有在白名單內。

若是第三方函式庫（例如 jQuery 的 `$`），或是自訂義全域物件，就無法在 Mustache 語法中使用。