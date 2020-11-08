---
title: "資料無法響應"
date: 2020-10-11
description: "Vue 資料無法響應的情況。"
keywords: ["Vue","Vue資料無法響應","竹白"]
tags: ["Vue"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/base/noRespond.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

Vue 資料無法響應的情況。

<!--more-->
{{< featuredImage >}}

## 對於物件

Vue 建立實體時，會對 `data` 物件中的所有屬性加入到 Vue 的 **響應式系統** 中，從而讓 `data` 的屬性能夠響應式更新。

但是 Vue 不能檢測到物件的新增或刪除。

舉例來說，我們如果想要在 Vue 實體初始化後新增 `count` 資料屬性：
```javascript
const vm = new Vue({
  el: '#app',
  data: {},
  created() {
    this.count = 0;
  }
});
```
`count` 雖然有被新增，但無法畫面無法隨著資料改變響應。

新增一個按鈕來改變 `count` 的值來驗證：
```html
<div id="app">
  <p>{{ count }}</p>
  <button @click="count += 1; alert(count)">+1</button>
</div>
```
**CodePen Demo：**[Vue2.x - 資料無法響應 - 對於物件](https://codepen.io/CHUPAIWANG/pen/YzqvjLz)


Vue 不允許動態添加根級別的屬性，所以一般情況下，我們不會這樣新增資料屬性，一定是預先定義資料屬性。

但是，如果資料屬性為物件，就有可能會發生以下情況。

我們在進行開發的過程中，不可能每次都預先定義好物件內的所有屬性。舉例來說，我們透過 AJAX 取得資料，但如果該資料沒有前端畫面要的屬性，就需要自己新增屬性。

這時就必須使用到 [`$set()`](https://cn.vuejs.org/v2/api/#vm-set)，來確保這個新屬性同樣是響應式的。

```javascript
const vm = new Vue({
  data: {
    use: {},
  },
  created() {
    // 取得資料
    this.use = {
      name: 'Peter',
    };
    // 新增屬性
    this.$set(this.use, 'follow', false);
  },
});
```

`$set()` 語法：
```javascript
vm.$set(target, propertyName/index, value)
```

## 對於陣列

### 1. 可觀察到的陣列方法

Vue 有重新對以下陣列方法進行包裝，因此可以觀察到使用以下方法的變化：
- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

### 2. 無法響應式變化

Vue 不能檢測以下陣列的變動：
1. 利用索引設置一個修改或新增元素。
2. 修改陣列長度。

舉例來說，我們將陣列的第一個元素值替換掉：
```html
<button @click="changeValue(array)">change</button>
<p>{{ array }}</p>
```
```javascript
data: {
  array: [0, 1, 2]
},
methods: {
  changeValue(arr) {
    arr[0] = 'zero';
  },
},
```
執行後，你會發現畫面沒有變動。

改用 `$set(array, index, value)` 即可觸發狀態更新：
```javascript
methods: {
  changeValue(arr) {
    this.$set(arr, 0 , 'zero');
  },
},
```

或者使用 `splice` 方法來修改或新增元素：
```javascript
arr.splice(0, 1, 'zero');
```

**CodePen Demo：**[Vue2.x - 資料無法響應 - 對於陣列](https://codepen.io/CHUPAIWANG/pen/KKpXdYz)
