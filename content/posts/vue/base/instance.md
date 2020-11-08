---
title: "Vue 實體"
date: 2020-10-06
description: "Vue 的實體（Instance）是 Vue.js 的核心，每個 Vue App 都是從建立實體開始。"
keywords: ["Vue","Vue實體","竹白"]
tags: ["Vue", "Vue實體"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/base/instance.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

Vue 的實體（Instance）是 Vue.js 的核心，每個 Vue App 都是從建立實體開始。

<!--more-->
{{< featuredImage >}}

## 建立 Vue 實體

Vue 實體是透過 Vue Constructor（建構式）所產生，在實體化時，可傳入一個選項物件（Options）。

```javascript
const vm = new Vue({
  // options
});
```

儲存 Vue 實體的變數名稱常見的縮寫有 `vm`（ViewModel 視圖模型）和 `app`（application 應用程式）。

若之後沒有要操作這個實體的狀態，可以不指定變數直接使用。

## 選項

傳入 Vue 實體的選項物件包含這個 Vue 實體需要用到的屬性，例如：
- `el`：用來掛載 Vue 實體元素
- `data`：要綁定的資料/狀態
- `methods`：定義在 Vue 實體內使用的方法（函式）
- `watch`：用來觀察 Vue 實體內資料的變動
- `props`：元件接收外部資料的屬性
- 等等...

## 掛載 DOM 元素

[`el`](https://cn.vuejs.org/v2/api/#el) 屬性為 `element` 縮寫，作用為掛載 Vue 實體與 DOM 元素的關係（此元素為根元素），是 **Vue App 的作用範圍**。

```html
<div id="app"></div>
```
```javascript
const vm = new Vue({
  el: '#app',
});
```
`el` 類型為 `string` 或 `Element`，也就是 CSS 選擇器或 DOM 物件。

`el` 只在用 `new` 創建實體時生效。在實體掛載後，元素可以用 [`vm.$el`](https://cn.vuejs.org/v2/api/#vm-el) 訪問。
```javascript
// Console
vm.$el;  // <div id='app'></div>
```

除了透過選項物件 `el` 屬性掛載 DOM 外，我們還能透過 [`$mount()`](https://cn.vuejs.org/v2/api/#vm-mount) 進行手動掛載。

```javascript
const vm = new Vue({});

vm.$mount('#app');
```

**CodePen Demo：**[Vue 2.x - Instance](https://codepen.io/CHUPAIWANG/pen/gOrevOy)