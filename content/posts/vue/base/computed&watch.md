---
title: "計算 & 監聽"
date: 2020-10-09
description: "資料計算 與 資料監聽。"
keywords: ["Vue","Vue實體","竹白"]
tags: ["Vue", "computed", "watch"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/base/computed&watch.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## Computed 計算

在專案開發中，資料往往需要經過一些處理，Vue 提供了 [`computed`](https://cn.vuejs.org/v2/api/#computed) 選項，用來減輕模板上的資料處理，方便重複利用及維護。

### 1. computed

以反轉字串為例：
```html
<div id="app">
  <p>{{ message.split('').reverse().join('') }}</p>
</div>
```
若是在 Mustache 中寫一些簡單運算還能馬上理解。但像上面寫一長串程式碼，我們可能無法立刻理解它的邏輯。

因此，我們可以使用計算屬性，將程式碼改寫成這樣：
```html
<div id="app">
  <p>{{ reversedMessage }}</p>
</div>
```
```javascript
const vm = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue.js!',
  },
  computed: {
    reversedMessage() {
      return this.message.split('').reverse().join('');
    },
  },
});
```
這樣不但方便重複使用與維護，也能馬上明白這段程式碼的意思。

<iframe height="200" style="width: 100%;" scrolling="no" title="Vue 2.x - Computed" src="https://codepen.io/CHUPAIWANG/embed/poyLQbL?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/poyLQbL'>Vue 2.x - Computed</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

{{< notice warning >}}
另外要注意，因為 Vue 會將 `computed` 整合到實體中，因此不要與 `data`、`methods` 重複宣告。
{{< /notice >}}

### 2. 與 method 的差異

雖然使用 `method` 一樣可以達到同樣的效果：
```html
<div id="app">
  <p>{{ reversedMessage() }}</p>
</div>
```
```javascript
methods: {
  reversedMessage() {
    return this.message.split('').reverse().join('');
  },
}
```
但**計算屬性會將結果緩存**，只在相關響應式依賴發生改變時它們才會重新計算。另外計算屬性沒辦法傳入參數。

響應式依賴發生改變是什麼意思呢？就是只有在計算屬性中使用被加到響應式系統的資料發生改變，計算屬性才會重新計算。

舉例來說，`Date.now()` 並不是響應式依賴，所以並不會重新計算。：
```javascript
computed: {
  now: function () {
    return Date.now()
  }
}
```

還有一點要注意，計算屬性要有使用才會計算，並不是依賴發生改變就重新計算。舉例來說，我們沒有在畫面上使用到 `c1`：
```javascript
const vm = new Vue({
  el: '#app',
  data: {
    message: 'Hello',
  },
  computed: {
    c1() {
      console.log('c1');
      return this.message + 1
    },
  },
});
```
```html
<div id="app"></div>
```
我們在 Console 中改變 `message` 的值，並不會印出 `'c1'`，也就是說 `c1` 並沒有被呼叫：
```javascript
// Console
vm.message = 'Hi!';
```
但是如果你有使用 [Vue.js devtools](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)，它會全部呼叫一次才能顯示值給你，因此就算沒到使用也會呼叫。

回到剛提到的 `method`，我們從以下程式碼，就可以觀察到兩者的差異：
```html
<div id="app">
  <p>{{ rmComputed }}</p>
  <p>{{ rmComputed }}</p>
  <p>{{ rmComputed }}</p>
  <p>{{ rmMethods() }}</p>
  <p>{{ rmMethods() }}</p>
  <p>{{ rmMethods() }}</p>
</div>
```
```javascript
const vm = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue.js!',
  },
  computed: {
    rmComputed() {
      console.log('computed');
      return this.message.split('').reverse().join('');
    },
  },
  methods: {
    rmMethods() {
      console.log('methods');
      return this.message.split('').reverse().join('');
    },
  },
});
```
```
// Console
'computed'
'methods'
'methods'
'methods'
```

我們在模板中重複訪問，你可以發現 `rmComputed` 只執行一次，而 `rmMethods` 會執行三次。

因為，只要跟 `rmComputed` 有關的 `message` 不變，就不會重新計算，會從緩存取得結果值。

### 3. setter

計算屬性函式型式預設為 getter，也可以改寫成物件型式新增一個 setter，當計算屬性被回寫 setter 就會被呼叫。

```javascript
const vm = new Vue({
  computed: {
    a: {
      get() {
        return 10;
      },
      set(newVal) {
        console.log('123');
      },
    },
  },
});

vm.a = 0;  // '123' ( a 被改變，所以執行 console.log('123')
```

{{< notice warning >}}
注意，只有使用 setter 時，才可以回寫計算屬性。否則開發環境版本的 Vue 會在 Console 跳出警告。
{{< /notice >}}

我們來看一個匯率轉換的應用：
```html
<div id="app">
  <p>假設 1 美元 = 29.8 台幣</p>
  <p>美元
    <input v-model.number="usd" type="number" />
  </p>
  <p>新台幣
    <input v-model.number="twd" type="number" />
  </p>
</div>
```
```javascript
const vm = new Vue({
  el: '#app',
  data: {
    usd: 1,
  },
  computed: {
    twd: {
      get() {
        return this.usd * 29.8;
      },
      set(newVal) {
        this.usd = newVal / 29.8;
      },
    },
  },
});
```
當 `twd` 變化，就會呼叫 setter 改變 `usd` 的值。

<iframe height="250" style="width: 100%;" scrolling="no" title="Vue2.x - 匯率轉換 computed" src="https://codepen.io/CHUPAIWANG/embed/oNxqVWg?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/oNxqVWg'>Vue2.x - 匯率轉換 computed</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


### 4. 總結

計算屬性的應用：
1. 分離邏輯
2. 緩存結果
3. 雙向綁定（setter 應用）

## Watch 監聽

Vue 提供了一種更通用的方式來觀察和響應 Vue 實體上的資料變動，那就是 [`watch`](https://cn.vuejs.org/v2/api/#watch) 選項。

### 1. watch 

監聽資料，當資料發生改變，我們就做某件事：
```javascript
const vm = new Vue({
  el: '#app',
  data: {
    message: '',
    count: 0,
  },
  watch: {
    message() {
     this.count += 1;
    },
  },
});
```
```html
<div id="app">
  <input type="text" v-model="message" >
  <p>{{ count }}</p>
</div>
```
當 `message` 發生變化，就計數。

<iframe height="200" style="width: 100%;" scrolling="no" title="Vue 2.x - Watch" src="https://codepen.io/CHUPAIWANG/embed/BayOEEX?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/BayOEEX'>Vue 2.x - Watch</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 2. 參數

當監聽的資料發生變化，這個監聽函式會傳入兩個參數，分別為 `newVal` 與 `oldVal`，變化後的新值、變化前的舊值。

```javascript
watch: {
  message(newVal, oldVal) {
    // do Something
  },
}
```

<iframe height="200" style="width: 100%;" scrolling="no" title="Vue 2.x - Watch : newVal, oldVal" src="https://codepen.io/CHUPAIWANG/embed/YzXWJVz?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/YzXWJVz'>Vue 2.x - Watch : newVal, oldVal</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 3. immediate

監聽屬性除了函式，也可以寫成物件型式：
```javascript
watch: {
  message: {
    handler(newVal, oldVal) {
      // do Something
    },
  },
}
```

物件型式有兩個選項可以設定，分別為 `immediate` 與 `deep`。

資料若沒變動，就不會觸發監聽函式，若希望資料初始值也會執行 watch 的動作，我們可以將 `immediate` 設為 `true`。

直接看應用範例，UNIX timestamp 格式轉換：
```javascript
const vm = new Vue({
  el: '#app',
  data: {
    date: new Date().toLocaleDateString(),
    timestamp: null,
  },
  watch: {
    date: {
      immediate: true,
      handler() {
        this.timestamp = this.turnTimestamp(this.date);
      },
    },
  },
  methods: {
    turnTimestamp(date) {
      const timestamp = Math.floor(new Date(date) / 1000);
      return timestamp;
    },
  },
});
```
```html
<div id="app">
  <input type="date" v-model="date" >
  <p>Date: {{ date }}</p>
  <p>UNIX timestamp: {{ timestamp }}</p>
</div>
```

如果沒使用 `immediate: true`，就需要在 `created` 鉤子上初始化 `timestamp`：
```javascript
created() {
  this.timestamp = this.turnTimestamp(this.date);
}
```

<iframe height="265" style="width: 100%;" scrolling="no" title="Vue 2.x - Watch : immediate" src="https://codepen.io/CHUPAIWANG/embed/jOEvoKw?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/jOEvoKw'>Vue 2.x - Watch : immediate</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


### 4. 深度監聽

如果 `data` 是物件，要監聽裡面的屬性變化，該怎麼做呢？

物件型式的另一個選項為 `deep` 深度監聽，設為 `true` 即可，只要其中一個屬性值發生變化，就會執行指定動作。

```javascript
data:{
  object: {
    a: '',
    b: '',
  }
},
watch: {
  object: {
    deep: true,
    handler() {
      // do Something
    },
  },
}
```

## 5. 總結

任何可以使用 `computed` 的功能，都能使用 `watch` 完成，因此很容自造成濫用 `watch`。

簡單的區分使用場景：
1. 簡單且單純的資料操作並用於畫面上，盡量使用 `computed`；
2. 如果需觸發行為再使用 `watch`。

非同步操作就只能使用 `watch`。