---
title: "資料 & 方法"
date: 2020-10-07
description: "Vue 的資料與方法。"
keywords: ["Vue","竹白"]
tags: ["Vue"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/base/instance.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

Vue 的資料與方法。

<!--more-->
{{< featuredImage >}}

## Data 資料

資料 [`data`](https://cn.vuejs.org/v2/api/#data) 選項是用來儲存 Vue 實體的資料/狀態。接受一個物件或是 `function` 形式的物件（元件限制）。

### 1. data

當一個 Vue 實體被建立時，它會將 `data` 物件中的所有屬性加入到 Vue 的 **響應式系統** 中，從而讓 `data` 的屬性能夠響應式更新。

舉例來說，我們定義個資料屬性 `message`：
```javascript
const vm = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue.js!',
  },
});
```
這個資料屬性就可以在實體對應的 Vue App 中進行使用。

我們能用 Vue 的模板語法 Mustache 將資料呈現在畫面上：
```html
<div id="app">
  <p>{{ message }}</p>
</div>
```

若資料屬性的值發生改變時，畫面將會產生「響應」，即匹配更新為新的值。

例如，新增一個用來改變 `message` 值的按鈕：
```html
<button @click="message = 'Hi!'">Change</button>
```

<iframe height="250" style="width: 100%;" scrolling="no" title="Vue 2.x - Data" src="https://codepen.io/CHUPAIWANG/embed/mdPxxEE?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/mdPxxEE'>Vue 2.x - Data</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

{{< notice warning >}}
但是，只有當實體被建立時，就已經存在於 `data` 中的屬性才是**響應式**的，這部分我們會在 **[資料無法響應](/posts/vue/base/noRespond)** 中說明。
{{< /notice >}}

### 2. 訪問資料

Vue 實體建立之後，我們可以使用 [`$data`](https://cn.vuejs.org/v2/api/?#vm-data) 來訪問資料物件。

另外 Vue 實體也代理了 `data` 物件上所有的屬性，因此訪問 `vm.message` 等同訪問 `vm.$data.message`。

```javascript
// Console
vm.message === vm.$data.message;  // true
```

### 3. Object.freeze()

Vue 會遍歷 `data` 中的所有屬性，並使用  `Object.defineProperty` 將屬性做 getter、setter 改造，以便監控狀態的更新（響應式更新）。

```javascript
const data = { message: 'Hello Vue.js!' };

const vm = new Vue({
  data,
});

conosle.log(data);
```
我們印出來觀察，就會發現資料物件已經不是原來的普通物件：

![](https://i.imgur.com/IRyp2eS.png)


若是使用 [`Object.freeze()`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze) 將物件凍結，這會阻止 Vue 修改屬性，也就說它將不會響應式更新。

這有什麼好處？若今天有一筆資料，只是純展示用的，根本不需要對它進行監聽，這時就可以使用 `Object.freeze()` 來提升性能。另一種做法是，將這筆資料放進 `computed` 屬性中，因為 `computed` 屬性只有依賴的響應式屬性變化才會重新計算。

有人會問，為什不直接放在全域呢？因為放在全域就不能用於模板語法上了。

### 4. 內建屬性

Vue 內建的實體屬性與方法。它們都有前綴 `$` 以便與用戶定義的屬性區分開來。

因此在 `data` 資料物件內，以 `_` 或 `$` 開頭的屬性不會被 Vue 實體代理，因為它們可能衝突。


- [實體屬性](https://cn.vuejs.org/v2/api/#实例属性)
- [實體方法 / 資料](https://cn.vuejs.org/v2/api/#实例方法-数据)
- [實體方法 / 事件](https://cn.vuejs.org/v2/api/#实例方法-事件)
- [實體方法 / 生命週期](https://cn.vuejs.org/v2/api/#实例方法-生命周期)


## Methods 方法

[`methods`](https://cn.vuejs.org/v2/api/?#methods) 選項，就是用來存放函式的，也就是方法，和一般的函式沒有區別。

### 1. methods

定義方法：
```javascript
const vm = new Vue({
  el: '#app',
  data: {
    a: 1,
    b: 2,
  },
  methods: {
    add() {
      return this.a + this.b;
    },
  },
});
```
這裡的 `this` 自動綁定為 Vue 實體（也就是 `vm`），因此可以透過 `this` 取得 `data` 資料物件內的屬性。

我們也可以直接在 Mustache 語法中呼叫方法：
```html
<div id="app">
  <p>{{ add() }}</p>
</div>
```
雖然在 Mustache 語法中使用方法，也會根據依賴的響應式屬性變化重新計算，但它沒有緩存，所以重複使用會重複計算。

**CodePen Demo：**[Vue 2.x - Methods](https://codepen.io/CHUPAIWANG/pen/rNedqjG)

{{< notice warning >}}
`methods` 基本上沒什麼特別的，唯一要注意的，就是不要使用箭頭函式（arrow function）定義方法，因為會改變 `this` 的指向。
{{< /notice >}}

更多關於 `methods` 應用，會在事件綁定中提到。

### 2. 訪問方法

`methods` 中的方法將被混入到 Vue 實體中，可以直接訪問這些方法：
```javascript
vm.add();
```

所以我們要在另一個方法中呼叫其他方法，也是使用 `this`。

```javascript
methods: {
  foo() {
    // ...
  },
  boo() {
    this.foo();
  },
}
```