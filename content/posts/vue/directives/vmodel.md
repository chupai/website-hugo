---
title: "表單資料的雙向綁定"
date: 2020-10-16
description: "Vue 表單資料的雙向綁定。"
keywords: ["Vue","Vue指令","竹白"]
tags: ["Vue", "Vue指令"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/directives/vmodel.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## 表單綁定

在表單元素中，要監聽使用者輸入的資料並更新畫面，需要同時做兩件事。

請考慮下面程式碼：
```html
<input type="text" :value="message" @input="onInput" />
<p>Message is : {{ message }}</p>
```
```javascript
data: {
  message: ''
},
methods: {
  onInput(event) {
    this.message = event.target.value;
  }
}
```
1. 將 `<input>` 的 `value` 特性綁定 `message`；
2. 並監聽 `<input>`，當事件觸發時，更新 `message`。

Vue 提供了 [`v-model`](https://cn.vuejs.org/v2/api/?#v-model) 語法糖，簡化了上述表單雙向綁定的操作。
```html
<input type="text" v-model="message">
<p>Message is : {{ message }}</p>
```
```javascript
data: {
  message: ''
}
```

當輸入文字框內的內容改變時，就會同步更新 `message`。

<iframe height="200" style="width: 100%;" scrolling="no" title="表單資料的雙向綁定 v-model" src="https://codepen.io/CHUPAIWANG/embed/yLLqJbv?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/yLLqJbv'>表單資料的雙向綁定 v-model</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## v-model

`v-model` 指令可以在表單元素上進行資料的雙向綁定。

會根據表單控制類型使用不同的特性並拋出不同的事件：
- `text` 和 `textarea` 元素使用 `value` 特性和 `input` 事件；
- `checkbox` 和 `radio` 使用 `checked` 特性和 `change` 事件；
- `select` 將 `value` 作為 prop 並將 `change` 作為事件。

另外，所有表單元素只要有綁定 `v-model`，就會忽略 `value`、`checked`、`selected` 等特性的設定。預設狀態會根據綁定資料的預設值顯示。

{{< notice success >}} 
對於需要使用輸入法編輯器（Input Method Editor, IME）的語言（如中文、日文、韓文等），你會發現 `v-model` 不會在輸入法組合文字過程中得到更新。因為 Vue 有使用 [Composition Event](https://developer.mozilla.org/en-US/docs/Web/API/CompositionEvent) 進行處理，你可以在 **[原始碼](https://github.com/vuejs/vue/blob/v2.6.10/src/platforms/web/runtime/directives/model.js)** 中看到。
{{< /notice >}}
## 輸入文字框

- `<input type="text">`：單行輸入文字框
- `<textarea>`：多行輸入文字框

單行輸入文字框就如開頭的範例所示，多行輸入文字框則與它一模一樣：
```html
<textarea v-model="message" ></textarea>
<p>Message is : {{ message }}</p>
```
```javascript
data: {
  message: ''
}
```

## 單選的按鈕欄位 radio

`radio` 只能單選，綁定的資料為字串。

```html
<input id="a" v-model="picked" type="radio" value="a" />
<label for="a">A</label>
<br />
<input id="b" v-model="picked" type="radio" value="b" />
<label for="b">B</label>
<br />
<input id="c" v-model="picked" type="radio" value="c" />
<label for="c">C</label>
<br />
<p>Picked: {{ picked }}</p>
```
```javascript
data: {
  picked: 'a'
},
```

<iframe height="200" style="width: 100%;" scrolling="no" title="表單資料的雙向綁定 v-model  選項" src="https://codepen.io/CHUPAIWANG/embed/rNNrqXY?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/rNNrqXY'>表單資料的雙向綁定 v-model  選項</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


## 可複選的核取方塊欄 checkbox

`checkbox` 綁定的資料為布林值或陣列。

### 1. 布林值

`checkbox` 單獨使用時，`v-model` 會綁定一個布林值：
```html
<input id="checkbox" v-model="checked" type="checkbox" />
<label for="checkbox">{{ checked }}</label>
```
```javascript
data: {
  checkbox: true
},
```
- 當資料預設值為 `true` 時，`checkbox` 會是勾選狀態，反之 `false` 則為未勾選狀態；
- 當改變 `checkbox` 勾選狀態時，資料會隨著勾選狀態改變成 `true` 或 `false`；

<iframe height="200" style="width: 100%;" scrolling="no" title="表單資料的雙向綁定 v-model  - checkbox" src="https://codepen.io/CHUPAIWANG/embed/rNNrqae?height=265&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/rNNrqae'>表單資料的雙向綁定 v-model  - checkbox</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 2. 陣列

如果是多個 `checkbox` 複選欄，`v-model` 要綁定一個陣列：
```html
<input id="jack" v-model="checkedNames" type="checkbox" value="Jack" />
<label for="jack">Jack</label>
<input id="john" v-model="checkedNames" type="checkbox" value="John" />
<label for="john">John</label>
<input id="mike" v-model="checkedNames" type="checkbox" value="Mike" />
<label for="mike">Mike</label>
<br />
<p>{{ checkedNames }}</p>
```
```javascript
data: {
  checkedNames: [],
},
```
勾選時，會根據勾選順序，將該值加入到陣列中。

<iframe height="250" style="width: 100%;" scrolling="no" title="表單資料的雙向綁定 v-model  - checkbox" src="https://codepen.io/CHUPAIWANG/embed/MWWBPaa?height=265&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/MWWBPaa'>表單資料的雙向綁定 v-model  - checkbox</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


### 3. 值綁定

單獨使用一個 `checkbox` 時，如果要自定義勾選與未勾選時的資料值，可以用 `true-value` 及 `false-value` 特性：
```html
<input id="checkbox" v-model="checked" type="checkbox" true-value="要" false-value="不要" />
<label for="checkbox">{{ checked }}</label>
```
```javascript
data: {
  checked: '要',
},
```

<iframe height="200" style="width: 100%;" scrolling="no" title="表單資料的雙向綁定 v-model  動態資料綁定 - checkbox" src="https://codepen.io/CHUPAIWANG/embed/qBBygQY?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/qBBygQY'>表單資料的雙向綁定 v-model  動態資料綁定 - checkbox</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

{{< notice error >}}
如果要用來提交表單，請改用單選的按鈕。因為這裡的 `true-value` 和 `false-value` 特性並不會影響 `value` 特性。
{{< /notice >}}


## 下拉選單 select

### 1. 單選

使用下拉選單 `<select>` 時，`v-model` 是綁定字串：
```html
<select v-model="selected">
  <option disabled value="">Please select one</option>
  <option value="A">A</option>
  <option value="B">B</option>
  <option value="C">C</option>
</select>
<span>Selected: {{ selected }}</span>
```
```javascript
data: {
  selected: ''
},
```
- 有設定 `value` 特性，資料會存入 `value` 值；
- 若是沒有，會將內容當作成 `value` 值存入。但是不建議這麼做。

<iframe height="265" style="width: 100%;" scrolling="no" title="表單資料的雙向綁定 v-model  下拉選單-單選" src="https://codepen.io/CHUPAIWANG/embed/dyyjwpX?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/dyyjwpX'>表單資料的雙向綁定 v-model  下拉選單-單選</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

如果 `v-model` 綁定資料的預設值未能匹配任何選項，`<select>` 將被渲染為**未選中**狀態。在 iOS 中，這會使用戶無法選擇第一個選項。因為這樣的情況下，iOS 不會觸發 `change` 事件。因此，推薦像上面範例一樣，提供一個值為空的禁用選項。

### 2. 複選

下拉選單加上 `multiple` 特性會變成可以複選，會改成綁定陣列：
```html
<select v-model="selected" multiple>
  <option>A</option>
  <option>B</option>
  <option>C</option>
  <option>D</option>
</select>
<br>
<span>Selected: {{ selected }}</span>
```
```javascript
data: {
  selected: [],
},
```
<iframe height="265" style="width: 100%;" scrolling="no" title="表單資料的雙向綁定 v-model  下拉選單-複選" src="https://codepen.io/CHUPAIWANG/embed/GRZYydo?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/GRZYydo'>表單資料的雙向綁定 v-model  下拉選單-複選</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## 修飾符

- `.lazy`：將預設的 `input` 事件改為監聽 `change` 事件。
- `.number`：輸入字串轉為有效的數字。
- `.trim`：輸入首尾空格過濾。

### 1. lazy

使用者每次輸入就會觸發 `input` 事件，若不希望資料更新次數這麼頻繁，可以使用 `.lazy` 將監聽改成 `change` 事件。
```html
<input type="text" v-model.lazy="message">
```
改成這樣子，只有離開輸入框的焦點時，資料才會更新，顯示資料將不會是及時的。

<iframe height="180" style="width: 100%;" scrolling="no" title="表單資料的雙向綁定 v-model - lazy" src="https://codepen.io/CHUPAIWANG/embed/GRZYyBZ?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/GRZYyBZ'>表單資料的雙向綁定 v-model - lazy</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 2. number

使用輸入框取得的值都是字串，就是使用 `number` 類型的輸入框也一樣，這麼一來，取得資料後還需要將值轉成數值型別才能做數值操作。

因此我們可以使用 `.number` 讓 Vue 幫我們轉型：
```html
<input type="number" v-model.number="number1">
＋
<input type="number" v-model.number="number2">
＝
{{ sum }}
```
```javascript
data: {
  number1: 0,
  number2: 1,
},
computed: {
  sum() {
    return this.number1 + this.number2;
  }
}
```
<iframe height="200" style="width: 100%;" scrolling="no" title="表單資料的雙向綁定 v-model - number" src="https://codepen.io/CHUPAIWANG/embed/YzqJYMy?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/YzqJYMy'>表單資料的雙向綁定 v-model - number</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 3. trim

使用 `.trim` 字串會執行 [`trim()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/Trim)，自動過濾首尾空白字元。