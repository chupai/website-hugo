---
title: "v-bind 特性綁定"
date: 2020-10-13
description: "Vue 特性綁定。"
keywords: ["Vue","Vue指令","竹白"]
tags: ["Vue", "Vue指令"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/directives/vbind.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

##  特性綁定

HTML 元素標籤的特性（attribute）無法使用 Mustache 語法與 `vue` 實體綁定，因此需要使用 `v-bind` 指令來綁定元素與 `vue` 實體的關係。因為常用到，所以有縮寫「`:`」。

{{< notice success >}} 
物件的 property 和 HTML attribute 中文都稱作「屬性」。筆記中會以「特性」表示 HTML attribute 來區分兩者。
{{< /notice >}}

### 1. 基本語法

[`v-bind`](https://cn.vuejs.org/v2/api/#v-bind) 語法：
```html
<!-- 語法 -->
<div :v-bind:attribute="expression"></div>

<!-- 簡寫 -->
<div :attribute="expression"></div>

<!-- 動態特性名（2.6.0 新增）： -->
<!-- 語法 -->
<div :v-bind:[attribute]="expression"></div>

<!-- 簡寫 -->
<div :[attribute]="expression"></div>
```
- `attribute`：要綁定的特性名稱。
- `expression`：預期值為 `any`（任何 `type`）。
- 2.6 版後新增了特性名稱加上方括號，也能是表達式。

舉例來說，動態地新增一個圖片連結：
```javascript
data: {
  imgSrc: 'https://picsum.photos/200/200'
}
```
```html
<img v-bind:src="imgSrc" alt="">
```
當 `imgSrc` 內容發生變化，顯示圖片就會更新。

### 2. 樣式綁定

操作元素的 class 列表和 style 行內樣式是資料綁定的常見需求，因為都是特性所以可以使用 `v-bind` 來處理。

雖然透過表達式計算出字串結果即可，但字串拼接麻煩且易錯，因此 Vue 對它們做了專門增強的功能，也就是表達式的結果值除了字串還可以是陣列或物件。

如果是其他特性，預期值傳入陣列或物件，會被強制轉型成字串。


## class 綁定

### 1. 物件語法

傳一個物件，以動態地切換 `class`。
```html
<div v-bind:class="{ className: boolean }"></div>
```
- `className`：類別名稱。
- `boolean`：布林值，切換類別。

它能與普通的 `class` 特性共存。

範例：
```css
/* 先定義 css */
.box {
  width: 100px;
  height: 100px;
  background: #222;
}

.red {
  background: red;
}
```
```javascript
data: {
  isRed: true,
}
```
```html
<div id="app">
  <div class="box" v-bind:class="{ red: isRed }"></div>
  <button v-on:click="isRed =!isRed">點擊</button>
</div>
```
如果 `isActive` 的值變成 `false`，`.red` 類別就會被移除，反之 `isActive` 的值 `true` 就會新增 `.red` 類別。

<iframe height="220" style="width: 100%;" scrolling="no" title="v-bind class" src="https://codepen.io/CHUPAIWANG/embed/RwwMZmm?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/RwwMZmm'>v-bind class</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

{{< notice warning >}} 
這裡注意的是，因為物件鍵名（key name）無法用 `-`，需要加上 `''` 單引號，明確的用字串當作鍵名。
{{< /notice >}}

舉例來說，`.box-red` 這個類別名稱，需要加上單引號，明確的用字串當作鍵名。
```html
<div class="box" v-bind:class="{ 'box-red': isActive }"></div>
```

綁定的物件不一定要定義在模板中：
```html
<div class="box" v-bind:class="classObject"></div>
```
```javascript
data: {
  classObject: {
    'box-red': false,
  },
}
```
但這樣我們變成需要操作 `classObject['box-red']` 這個屬性。

因此建議使用一個回傳物件的 `computed` 計算屬性，將這個物件包裝起來，方便管理維護。

```javascript
data: {
  isRed: true,
},
computed: {
  classObject() {
    return { 'box-red': this.isRed, }
  },
}
```

### 2. 陣列語法

傳給 `v-bind:class` 一組陣列，以應用一個 class 列表。
```html
<div v-bind:class="[ 'className1', 'className2' ]"></div>

<!-- HTML 將被渲染成 -->
<div class="className1 className2"></div>
```

結合 `checkbox` + `v-model`：
<iframe height="265" style="width: 100%;" scrolling="no" title="v-bind:class 陣列語法" src="https://codepen.io/CHUPAIWANG/embed/gOOVQrV?height=265&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/gOOVQrV'>v-bind:class 陣列語法</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

另外，陣列中也可以用物件語法：
```html
<div v-bind:class="[{ className1: true }, 'className2']"></div>
```


## style 綁定

### 1. 物件語法

直接舉例說明：
```html
<div v-bind:style="{ backgroundColor: '#ddd', width: '100px', height: '100px' }"></div>
```

語法雖然看起來像 CSS 樣式，但它其實是 JS 物件，因此要注意：
- CSS 屬性名，有兩種寫法：
  - 駝峰式（camelCase），`backgroundColor`
  - 短橫線分隔（kebab-case），`'background-color'`（需要明確的用字串當作鍵名，因武要加上單引號）
- 值（沒有預設單位，因此有單位的需使用字串）


多重值：
```html
<div v-bind:style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```
會渲染陣列中最後一個被瀏覽器支持的值。

### 2. 陣列語法

我們可以用陣列語法，將多個樣式物件應用在同一個元素上：
```html
<div v-bind:style="[baseStyles, bgStyles]"></div>
```
```javascript
data: {
  baseStyles: { 
    width: '100px',
    height: '100px'
  },
  bgStyles: { 'background-color': '#ddd'},
}
```

## 重複特性

### 1. 一般特性

如果使用 `v-bind` 和未使用 `v-bind` 特性重複，後寫的會將先寫覆蓋：
```javascript
data: {
  google: 'https://www.google.com/',
  yahoo: 'https://tw.yahoo.com/',
}
```
```html
<a href="https://www.google.com/" v-bind:href="yahoo" target="_blank">1</a>
<a v-bind:href="yahoo" href="https://www.google.com/" target="_blank">2</a>

<!-- HTML 將被渲染成 -->
<a href="https://tw.yahoo.com/" target="_blank">1</a>
<a href="https://www.google.com/" target="_blank">2</a>
```


### 2. 樣式綁定

但樣式綁定 class 和 style 會將兩者合併，互相不干擾。

上面範例已經用過了：
```html
<div class="box" v-bind:class="{ red: isRed }"></div>

<!-- HTML 將被渲染成 -->
<div class="box red"></div>
```

但是如果出現重複類別，要特別注意：
```html
<div class="box red" v-bind:class="{ red: isRed }"></div>

<!-- HTML 將被渲染成 -->
<div class="box red red"></div>
```
將會出現兩個相同的類別，我們只能控制使用 `v-bind` 的類別。


若是 `style` 出現相同 CSS 樣式，`v-bind` 會覆蓋原本的樣式：
```html
<div class="box" :style="{ backgroundColor: 'blue' }" style="background-color: red;"></div>
<div class="box" style="background-color: red;" :style="{ backgroundColor: 'blue' }"></div>

<!-- HTML 將被渲染成 -->
<div class="box" style="background-color: blue;"></div>
<div class="box" style="background-color: blue;"></div>
```

另外還有使用陣列語法的重複，若物件有相同 CSS 樣式：
```javascript
data: {
  red: { backgroundColor: 'red' },
  blue: { backgroundColor: 'blue' },
}
```
```html
<div class="box" :style="[blue, red]"></div>
<div class="box" :style="[red, blue]"></div>

<!-- HTML 將被渲染成 -->
<div class="box" style="background-color: red;"></div>
<div class="box" style="background-color: blue;"></div>
```
後者會覆蓋前者。