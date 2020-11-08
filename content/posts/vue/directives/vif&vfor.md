---
title: "條件渲染 & 列表渲染"
date: 2020-10-14
description: "Vue 條件渲染 & 列表渲染。"
keywords: ["Vue","Vue指令","竹白"]
tags: ["Vue", "Vue指令"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/directives/vif&vfor.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## 條件渲染

### 1. v-if

[`v-if`](https://cn.vuejs.org/v2/api/#v-if) 指令的表達式預期值為 `any`，若表達式結果為 `truthy` 值則顯示該元素。

舉例來說，若希望某區塊在特定條件下才出現：
```html
<div v-if="type === 'A'">A Content</div>
```
```javascript
data: {
  type: 'A',
}
```
如果 `type` 是 `A` 字串，`type === 'A'` 會是 `true`，那結果會是 `truthy` 值，則顯示這個元素，反之如果 `type` 不等於 `A` 字串，則不顯示元素。

若要連續判斷，可以使用 [`v-else-if`](https://cn.vuejs.org/v2/api/#v-else-if)：
```html
<div v-if="type === 'A'">A Content</div>
<div v-else-if="type === 'B'">B Content</div>
```

另外可以搭配 [`v-else`](https://cn.vuejs.org/v2/api/#v-else)，若結果為 `falsy` 值則顯示另一個元素：
```html
<div v-if="type === 'A'">A Content</div>
<div v-else-if="type === 'B'">B Content</div>
<div v-else>No Content</div>
```

要注意：
- `v-else-if` 的元素必須緊跟在帶 `v-if` 的元素的後面；
- `v-else` 的元素必須緊跟在帶 `v-if` 或者 `v-else-if` 的元素的後面，否則它將不會被識別。

不過，如果是純文字的輸出，容器元素相同，盡量別將複雜的邏輯判斷寫在 HTML 上面。

可以改用以下方式：
```javascript
const dataMapping = {
  A: 'A Content',
  B: 'B Content',
};

// vm 略 ...
data: {
  type: 'A',
},
computed: {
  content() {
    return dataMapping[this.type] || 'No Content';
  },
}
```
```html
<div>{{ content }}</div>
```


### 2. 切換多個元素

如果要切換多個元素，可使用 `<template>`，當做不可見的包裹元素。
```html
<template v-if="true">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>

<!-- HTML 將被渲染成 -->
<h1>Title</h1>
<p>Paragraph 1</p>
<p>Paragraph 2</p>
```
最終的渲染結果將不包含 `<template>` 元素。

### 3. v-show

[`v-show`](https://cn.vuejs.org/v2/api/#v-show) 一樣是用於條件顯示元素的指令，但不同是 `v-show` 的元素始終會被渲染並保留在 DOM 中，而是透過切換元素的 CSS `display` 屬性。

兩者的比較：
- `v-if` 是真正的條件渲染，會確保在切換過程中條件塊內的事件監聽器和子元件適當地被銷毀和重建；
- `v-if` 是**惰性的**，如果在初始渲染時條件為 `false`，則什麼也不做，直到條件第一次變為 `true`，才會開始渲染條件塊；
- 相比之下 `v-show` 不論條件為何都渲染，而是進行 CSS `display` 屬性切換；
- `v-show` 不支持 `<template>` 元素，也不支持 `v-else`；
- `v-if` 比起 `v-show` 切換時的開銷更高，因此需要非常頻繁地切換，則使用 `v-show` 較佳。

## 列表渲染

[`v-for`](https://cn.vuejs.org/v2/api/#v-for) 用於多次渲染元素，必須使用 `alias in expression` 特定語法。

也可以用 `of` 替代 `in` 作為分隔符號，兩者沒有差異，但 `of` 它更接近 JavaScript 迭代器的語法。

### 1. 陣列

使用 `v-for` 迭代陣列中的元素。

```html
<div v-for="(item, index) in array"></div>
```
- `array`：陣列。
- `item`：陣列元素。
- `index`：索引。
- 只使用第一個參數時，不需要括號。

直接看範例，假設我們要建立一組列表：
```javascript
data: {
  list: [
    'HTML',
    'CSS',
    'JavaScript',
    'Vue.js',
  ],
}
```
```html
<ul>
  <li v-for="item in list">{{ item }}</li>
</ul>

<!-- HTML 將被渲染成 -->
<ul>
  <li>HTML</li>
  <li>CSS</li>
  <li>JavaScript</li>
  <li>Vue.js</li>
</ul>
```

如果要使用第二個參數 `index`（索引值），需加上括號：
```html
<ul>
  <li v-for="(item, index) in array">
    {{ index + '. ' + item }}
  </li>
</ul>

<!-- HTML 將被渲染成 -->
<ul>
  <li>0. HTML</li>
  <li>1. CSS</li>
  <li>2. JavaScript</li>
  <li>3. Vue.js</li>
</ul>
```
<iframe height="200" style="width: 100%;" scrolling="no" title="v-for 列表" src="https://codepen.io/CHUPAIWANG/embed/ZEExjXd?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/ZEExjXd'>v-for 列表</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 2. 物件

`v-for` 也可以用來遍歷一個物件的屬性。

```html
<div v-for="(value, name, index) in object"></div>
```
- `object`：物件。
- `value`：鍵名。
- `name`：鍵值。
- `index`：遍歷順序。
- 只使用第一個參數時，不需要括號。

在遍歷一個物件時，會按 `Object.keys()` 的結果遍歷，但是不能保證它的結果在不同瀏覽器上的 JavaScript 引擎下都一致。

```javascript
data: {
  object: {
    name: 'Peter',
    age: '29',
    sex: 'male',
  },
}
```
```html
<ul>
  <li v-for="(value, name, index) in object">
    {{ index }}. {{ name }}: {{ value }}
  </li>
</ul>
```
<iframe height="200" style="width: 100%;" scrolling="no" title="v-for 物件" src="https://codepen.io/CHUPAIWANG/embed/zYYjqNj?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/zYYjqNj'>v-for 物件</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 3. 整數

`v-for` 也可以接受整數。

```html
<span v-for="index in number">{{ n }}</span>
```
- `number`：正整數
- `index`：起始從 `1` 開始，至 `number`

舉例來說：
```html
<div>
  <span v-for="n in 5">{{ n }}</span>
</div>

<!-- HTML 將被渲染成 -->
<div>
  <span>1</span>
  <span>2</span>
  <span>3</span>
  <span>4</span>
  <span>5</span>
</div>
```

印出九九乘法表：
```html
<ul>
  <li v-for="x in 9">
    <ul>
      <li v-for="y in 9">
        {{ x + 1 }} × {{ y }} ＝ {{ (x + 1) * y }}
      </li>
    </ul>
  </li>
</ul>
```

### 4. 重複多個元素

類似於 `v-if` 提到的用法，如果要重複渲染多個元素，可使用 `<template>`，當做不可見的包裹元素。

```html
<template v-for="item in items">
  <h2>{{ item.title }}</h2>
  <p>{{ item.content }}</p>
</template>
```

## 同時使用 v-if 和 v-for

### 1. 如果同時使用

當 `v-if` 與 `v-for` 一起使用時，`v-for` 具有比 `v-if` 更高的優先級。`v-if` 會隨著 `v-for` 重覆執行數次。

```javascript
data: {
  array: [1, 3, 0, 4, 5, 0, 2]
},
```

只渲染值為 `true` 的成員：
```html
<ul>
  <li v-for="item in array" v-if="item">
    {{ item }}
  </li>
</ul>

<!-- HTML 將被渲染成 -->
<ul>
  <li>1</li>
  <li>3</li>
  <li>4</li>
  <li>5</li>
  <li>2</li>
</ul>
```

但要注意，`v-for` 的個數範圍判斷條件成立後，才會輪到 `v-if` 來判斷顯示與否。請考慮以下範例：
```html
<ul>
  <li v-for="item in array" v-if="array.length !== 0">{{ item }}</li>
  <p v-else>Nothing could show.</p>
</ul>
```
```javascript
data: {
  array: []
}
```
目前 `array` 是空陣列，沒有任何項目可顯示，當 `v-for` 在判斷 `item in array` 時，發現條件不成立，因此就不會就不去做 `v-if` 和 `v-else` 判斷，會導致 Nothing could show. 不會出現。

可以將程式改成這樣：
```html
<ul>
  <li v-for="item in array">{{ item }}</li>
  <p v-if="array.length !== 0">Nothing could show.</p>
</ul>
```

### 2. 避免同時使用

{{< notice error >}} 
上面操作只會出現在範例說明，**[官方風格指南](https://cn.vuejs.org/v2/style-guide/#避免-v-if-和-v-for-用在一起必要)** 明確表示：避免 `v-if` 和 `v-for` 用在同一個元素上。
{{< /notice >}}

假設為了過濾一個列表中的項目：
```html
<!-- .html -->
<ul>
  <li
    v-for="user in users"
    v-if="user.isActive"
  >
    {{ user.name }}
  </li>
</ul>
```
```javascript
data: {
  users: [
    { name: 'A', isActive: true },
    { name: 'B', isActive: true },
    { name: 'C', isActive: false },
    { name: 'D', isActive: true },
  ],
```

此寫法會經過以下運算:
```javascript
this.users.map(function (user) {
  if (user.isActive) {
    return user.name
  }
})
```
由此可知，哪怕只需要渲染出一小部分 `user` 的元素，也得每次重新渲染時遍歷整個 `users` 列表，不論 `isActive` 狀態是否發生變化。

因此我們可以將上述程式碼稍作修改，將 `users` 替換為一個計算屬性：
```javascript
computed: {
  activeUsers() {
    return this.users.filter(function (user) {
      return user.isActive
    })
  }
}
```
```html
<ul>
  <li v-for="user in activeUsers">
    {{ user.name }}
  </li>
</ul>
```

程式碼改成這樣有以下優點：
- 過濾後的列表 `activeUsers`，只會在 `users` 陣列發生相關變化時才被重新運算，過濾更高效；
- 在渲染的時候，只遍歷篩選後的 `user`，渲染更高效；
- 解耦渲染層的邏輯，可維護性（對邏輯的更改和擴展）更強。

## key 屬性

### 1. 沒 key 會遇到的情況

請考慮以下程式碼：
```html
<div v-if="show">
  <label>Username</label>
  <input placeholder="username" />
</div>
<div v-else>
  <label>Email</label>
  <input placeholder="email" />
</div>
<button @click="show = !show">change</button>
```
```javascript
data: {
  show: true,
}
```

你可以試著在輸入框輸入，接著按切換按鈕，你會發現你輸入的值並不會消失，這表示它們共用同一個元素。

<iframe height="265" style="width: 100%;" scrolling="no" title="key 屬性 v-if" src="https://codepen.io/CHUPAIWANG/embed/VwaGMbW?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/VwaGMbW'>key 屬性 v-if</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


因此你需要加上替它們加上 `key` 屬性，使這兩個元素完全獨立，每次切換時輸入框才會重新渲染。

原因等等下面會說明，繼續看另一段程式碼：
```html
<ul>
  <li v-for="item in arrayData">
    {{ item.name }} <input type="checkbox">
  </li>
</ul>
<button @click="arrayData.reverse()">反轉陣列</button>
```
```javascript
data: {
  arrayData: [
    {
      id: 1,
      name: "test1",
    },
    {
      id: 2,
      name: "test2",
    },
    {
      id: 3,
      name: "test3",
    },
    {
      id: 4,
      name: "test4",
    },
  ],
}
```

當你點選 `checkbox` 後，再點選反轉陣列的按鈕，你可觀察到，`checkbox` 並不會隨著資料變換而跟著改變。

<iframe height="265" style="width: 100%;" scrolling="no" title="v-for" src="https://codepen.io/CHUPAIWANG/embed/XWWvOXm?height=265&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/XWWvOXm'>v-for</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 2. key 的作用

上面情況的原因是 Vue 在切換畫面時，並不會完全置換元素，會使用一種最大限度減少動態元素並且儘可能的嘗試修復/再利用相同類型元素的算法。但是 **不適用** 於需依賴子元件狀態、臨時 DOM 狀態的列表渲染輸出（例如，表單輸入）。

為了解決這個問題，可以使用 Vue 提供的 [`key`](https://cn.vuejs.org/v2/api/#key) 屬性，key 的作用是給予一個節點唯一的身份識別，有相同父元素的子元素必須有獨特的 key。這樣它可以前後對比，算出哪些節點是要重複使用或者調整順序。

```html
<li v-for="item in arrayData" :key="item.id">
```

<iframe height="265" style="width: 100%;" scrolling="no" title="v-for key" src="https://codepen.io/CHUPAIWANG/embed/OJJKdqY?height=265&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/OJJKdqY'>v-for key</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

{{< notice success >}} 
2.2.0+ 的版本，當在元件上使用 `v-for` 時，`key` 屬性現在是必須的。
{{< /notice >}}


### 2. 避免使用 index 作為 key 值

在 `v-for` 指令上，避免使用 `index` 當作 `key` 值。

請考慮以下程式碼：
```html
<ul>
  <li v-for="(item, index) in arrayData" :key="index">
    {{ item.name }} <input type="checkbox">
    <button @click="del(index)">del</button>
  </li>
</ul>
<button @click="arrayData.reverse()">反轉陣列</button>
```
```javascript
data: {
  arrayData: [
    {
      id: 1,
      name: "test1",
    },
    {
      id: 2,
      name: "test2",
    },
    {
      id: 3,
      name: "test3",
    },
    {
      id: 4,
      name: "test4",
    },
  ],
},
methods: {
  del(index) {
    this.arrayData.splice(index, 1);
  },
}
```
<iframe height="265" style="width: 100%;" scrolling="no" title="v-for key=&quot;index&quot;" src="https://codepen.io/CHUPAIWANG/embed/eYmOzry?height=265&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/eYmOzry'>v-for key=&quot;index&quot;</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

刪除前：

| key | index | name | id |
| -------- | -------- | -------- | -------- |
| 0 | 0 | test1 | 1 |
| 1 | 1 | test2 | 2 |
| 2 | 2 | test3 | 3 |
| 3 | 3 | test4 | 4 |

假設刪除 `test2` 後：

| key | index | name | id |
| -------- | -------- | -------- | -------- |
| 0 | 0 | test1 | 1 |
| 1 | 1 | test3 | 3 |
| 2 | 2 | test4 | 4 |

因為刪除了 `test2` 導致 `index` 發生變化，而 `key` 因為綁定 `index` 而導致 `test3` 與 `test4` 被重新渲染。而且 `key = 2` 的 `checkbox` 狀態會變成 `text3` 的。

{{< notice warning >}} 
另外官方文件也標示，`key` 值不可以是物件或陣列，必須是字串或數值。
{{< /notice >}}


### 3. 其他用途

`key` 屬性也可以用於強制替換元素/元件而不是重複使用它。如果遇到以下場景會非常用：
- 完整地觸發元件的生命週期鉤子
- 觸發轉場

舉例來說：
```html
<transition>
  <div v-if="toggle" :key="1">Hello</div>
  <div v-else key="2">Goodbye</div>
</transition>
```

當 切換 `toggle` 時，它也是切換 `<div>`，如果你想用上 enter/leave animations 時，即節點插入和刪除的時候的動畫時就要打破這個復用。