---
title: "Vue元件 - Prop"
date: 2020-10-20
description: "Vue 元件的狀態資料傳遞。"
keywords: ["Vue","Vue元件","竹白"]
tags: ["Vue", "Vue元件"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/component/prop.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## Prop

由於元件與外層（Vue 實體或父元件）屬於不同的實體，因此不能直接在元件上操作外層資料狀態，這時就需要透過 [`props`](https://cn.vuejs.org/v2/api/#props) 選項屬性，將資料從外層傳遞給元件。

### 1. 基本範例

假設 Vue 實體有一個資料屬性 `url` ，要將它傳給 `c-img` 元件使用。
```javascript
const vm = new Vue({
  el: '#app',
  data: {
    url: 'https://picsum.photos/200',
  },
});
```

首先，在元件的 `props` 選項定義一個 `prop`：
```javascript
Vue.component('c-img', {
  props: ['imgUrl'],
});
```

傳遞方式為，在 HTML 上使用自定義特性：
```html
<c-img :img-url="url"></c-img>
```

這樣我們就能在元件實體上使用這筆資料：
```javascript
Vue.component('c-img', {
  props: ['imgUrl'],
  template: '<img :src="imgUrl" alt="">',
});
```

<iframe height="300" style="width: 100%;" scrolling="no" title="Vue 2.x - 元件 Prop" src="https://codepen.io/CHUPAIWANG/embed/poyQroE?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/poyQroE'>Vue 2.x - 元件 Prop</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## Prop 的命名

HTML 中的特性是大小寫不敏感的，因此如果 Prop 的命名是 `camelCase`，在 HTML 中要轉成 `kebab-case`。 

```javascript
props: ['imgUrl']
```
```html
<photo img-url="https://picsum.photos/200"></photo>
```

但如果是 Prop 的命名是 `kebab-case`，表達式內要轉成 `camelCase`：
```javascript
props: ['img-url'],
template: '<img :src="imgUrl" alt="">',
```

## Prop 型別檢查

### 1. 物件形式

基本範例的 `props` 選項是以字串陣列的形式定義 `prop`：
```javascript
props: ['propA', 'propB', 'propC']
```

我們可以可以使用物件形式定義 `prop`，這樣能夠指定 `prop` 的型別限制：
```javascript
props: {
  // 基礎的型別檢查
  propA: String,
  // 多個可能的型別
  propB: [String, Number],
}
```
這裡的型別不是只有 JS 內建型別的七個型別，而是只要有原生建構函式都可以檢查，例如 `Date`、`Promise` 等等。

若是傳入不符合的型別，開發環境構建版本的 Vue 會在 Console 跳出警告。這在開發第三方元件時非常有用。

{{< notice warning >}}
但要注意，`null` 和 `undefined` 會符合所有型別查，不會跳出警告。
{{< /notice >}}

### 2. Prop 驗證

上面的寫法只能檢查型別，prop 還能使用物件的方式來定義。

物件規則說明如下：
- `type`：資料型別，可以是原生建構函式中的一個，或是自定義的建構函式。
- `default`：預設值。
- `required`：該屬性是否為必要。
- `validator`：自定義驗證函式，檢驗屬性資料是否有效。

```javascript
props: {

  // 一定要有值，而且要是字串
  propA: {
    type: String,
    required: true
  },
  
  // 帶有預設值
  propB: {
    type: Number,
    default: 100
  },
  
  // 物件或陣列預設值必須從一個工廠函式獲取
  propC: {
    type: Object,
    default() {
      return { message: 'hello' }
    }
  },
  
  // 自定義驗證函式
  propD: {
    validator(value) {
      // 這個值必須匹配下列字串中的一個
      return ['success', 'warning', 'danger'].indexOf(value) !== -1
    }
  }  
}
```

{{< notice error >}}
`prop` 驗證會在實體建立前執行，因此 `default` 和 `validator` 都不可以使用實體資料狀態（如 `data`、`computed` 等）。
{{< /notice >}}


## 傳遞靜態或動態資料

`prop` 可以傳入一個靜態的值，也可以透過 `v-bind` 動態賦值。

但要注意，靜態賦值傳入的結果值為字串，如果要傳數值、布林值、陣列、物件等等，需要用到 `v-bind` 才會是 JS 表達式。

舉例來說，傳入一個數值：
```html
<!-- 靜態 -->
<text-component text-content="42"></text-component>

<!-- 動態 -->
<text-component v-bind:text-content="42"></text-component>
```
靜態傳入的 42 是字串，不是數值，必須使用 v-bind 動態傳入。

### 1. 傳入一個布林值

如果指定一個 `prop` 是布林值：
- 那麼就算 `prop` 特性沒給值的情況下，它也會傳 `true`；
- 若是沒有設定特性，則會傳 `false`。

```html
<div id="app">
  <c-demo b></c-demo>
</div>
```
```javascript
Vue.component('c-demo', {
  props: {
    a: Boolean,
    b: Boolean,
  },
  created() {
    console.log(this.a);  // false
    console.log(this.b);  // true
  },
});

const vm = new Vue({
  el: '#app',
  data: {},
});
```
**CodePen Demo**：[Vue 2.x - 傳入一個布林值](https://codepen.io/CHUPAIWANG/pen/vYGQjmM)

### 2. 傳入一個物件的所有屬性

如果要傳入一個物件的所有屬性，可以使用不帶參數的 `v-bind`。

舉例來說，假設要將一個物件 `obj` 中的所有屬性，分別傳遞給一個元件，需要在元件 `props` 定義對應的屬性：
```javascript
// 外層資料
data: {
  obj: {
    aaa: 'a',
    bbb: 'b',
  },
}

// 元件 prop
props: ['aaa', 'bbb']
```

並且將對應的物件屬性傳入元件：
```javascript
<c-demo
  v-bind:aaa="obj.aaa"
  v-bind:bbb="obj.bbb"></c-demo>
```

可以使用不帶參數的 `v-bind` 傳入物件，會自動將物件解構傳入，等同上面傳入操作：
```html
<c-demo v-bind="obj"></c-demo>
```
**CodePen Demo**：[Vue 2.x - 傳入一個物件的所有屬性](https://codepen.io/CHUPAIWANG/pen/eYZQMqv)

{{< notice warning >}}
要注意，`v-bind` 這裡不能使用縮寫，因為沒綁定特性。
{{< /notice >}}

## 單向資料流（One-Way Data Flow）

元件的 `prop` 是單向綁定的，可由外向內傳遞資料，但不會反過來。主要是為了避免子元件任意更改父級的狀態，從而導致你的應用程式的資料流向難以理解。

{{< notice warning >}}
如果直接去改變 `prop`，開發環境版本的 Vue 會在 Console 跳出警告。
{{< /notice >}}

若我們有改變資料的需求，就必須使用 `data` 或 `computed` 儲存它：
```javascript
props: ['initialCounter'],
data() {
  return {
    counter: this.initialCounter
  }
}

// or
props: ['size'],
computed: {
  normalizedSize() {
    return this.size.trim().toLowerCase()
  }
}
```

{{< notice error >}}
如果外層傳入的資料是物件型別（物件、陣列），還是會因為傳址的特性，不小心更動到父級的資料狀態。因此盡量避免傳入物件型別的資料，確保資料的流向。
{{< /notice >}}

## 非 Prop 的特性

一個非 `prop` 的特性，指的是元件未定義的 `prop` 的定性。

### 1. 替換/合併

如果在元件 `tag` 上使用非 `prop` 的特性，它將被加到根元素上。

對於大部分的特性來說，若是元件根元素已經有相同的特性，特性值將會被替換掉：
```javascript
Vue.component('c-demo', {
  props: [],
  template: '<div data-text="5">demo</div>'
});
```
```html
<c-demo data-text="10"></c-demo>

<!-- 將被渲染成 -->
<div data-text="10">demo</div>
```

但對於樣式特性： 
- `class` 會智能的合併，不會出現重複或覆蓋類別；
- `style` 也會合併（根元素上的樣式再前），但如果有重複的 CSS 樣式，根元素上的樣式會被覆蓋。

### 2. 禁用繼承

請考慮以下情況。

建立一個 `checkbox` 元件，但根元素通常不會是 `<input>` 元素。如果在元件 `tag` 上使用 `checked="checked"` 一定無法作用：
```javascript
Vue.component('c-checkbox', {
  template: `
  <div>
    <input type="checkbox" id="checkbox">
    <label>要不要</label>
  </div>
  `,
});
```

因此，我們要關掉根元素繼承特性，需要在元件選項中設置 `inheritAttrs: false`：
```javascript
Vue.component('c-demo', {
  inheritAttrs: false,
  // ...
});
```
{{< notice warning >}}
注意 `inheritAttrs: false` 選項不會影響 `style` 和 `class` 的綁定。
{{< /notice >}}

設置完 `inheritAttrs: false` 後，要搭配 [`$attrs`](https://cn.vuejs.org/v2/api/#vm-attrs) 使用，它是一個物件，包含元件 `tag` 上所有非 Prop 的特性（`style` 和 `class` 除外）。

使用 `v-bind` 手動將它綁定要作用的元素上：
```javascript
Vue.component('c-checkbox', {
  template: `
  <div>
    <input type="checkbox" id="checkbox" v-bind="$attrs">
    <label>要不要</label>
  </div>
  `,
  inheritAttrs: false,
});
```
```html
<div id="app">
  <c-checkbox checked="checked"></c-checkbox>
</div>
```
**CodePen Demo**：[Vue 2.x - 非 Prop 的特性](https://codepen.io/CHUPAIWANG/pen/poyQOOr)

{{< notice warning >}}
注意，`checked="checked"` 有時會簡寫成 `checked`。但關閉繼承後，在 `$attrs` 物件內 `checked` 會得到空字串（因為沒給值），也就是說會變成不勾選狀態。因此在使用表單元素相關特性時，要多加留意。
{{< /notice >}}