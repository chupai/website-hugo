---
title: "事件綁定"
date: 2020-10-15
description: "Vue 事件綁定。"
keywords: ["Vue","Vue指令","竹白"]
tags: ["Vue", "Vue指令"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/directives/von.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## 事件綁定

事件是 JavaScript 操作網頁最重要的部分。在 Vue 主要是透過 [`v-on`](https://cn.vuejs.org/v2/api/#v-on) 指令來對需要監聽的 DOM 進行事件綁定，因為比較常用到，可以使用「`@`」來縮寫。

```html
<!-- 語法 -->
<button v-on:event="expression"></button>

<!-- 縮寫 -->
<button @event="expression"></button>
```
- `event`：事件名稱。
- `expression`：表達式，預期值為。
  - `Inline Statement`
  - `Function`
  - `Object`

### 1. 行內敍述

範例，一個按鈕綁定 `click`：
```html
<button v-on:click="count += 1">點我+1</button>
<p>{{ count }}</p>
```
```javascript
data: {
  count: 0,
},
```
<iframe height="180" style="width: 100%;" scrolling="no" title="v-on 行內敍述" src="https://codepen.io/CHUPAIWANG/embed/vYYrNRX?height=265&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/vYYrNRX'>v-on 行內敍述</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


### 2. 綁定事件處理函式

行內敍述只能用於簡單事件處理，因為你不可能將所有邏輯複雜的程式碼都寫在 `v-on` 指令中，或者遇到需要重複執行的情況。因此可以將事件處理函式用 `method` 宣告，並透過 `v-on` 綁定。

```html
<button v-on:click="addCount">點我+1</button>
<p>{{ count }}</p>
```
```javascript
data: {
  count: 0,
},
methods: {
  addCount() {
    this.count += 1;
  },
}
```

<iframe height="180" style="width: 100%;" scrolling="no" title="v-on 事件處理函式" src="https://codepen.io/CHUPAIWANG/embed/bGGKeaY?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/bGGKeaY'>v-on 事件處理函式</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 3. 傳遞參數

如果要在事件中傳遞參數，可以在行內敍述中直接呼叫事件處理函式：
```html
<button v-on:click="addCount(3)">點我</button>
<p>{{ count }}</p>
```
```javascript
data: {
  count: 0,
},
methods: {
  addCount(i) {
    this.count += i;
  },
}
```

<iframe height="180" style="width: 100%;" scrolling="no" title="v-on  在事件中傳遞參數" src="https://codepen.io/CHUPAIWANG/embed/ExxRgWK?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/ExxRgWK'>v-on  在事件中傳遞參數</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 4. 綁定多個事件

綁定多個事件，可以重複使用 `v-on` 指令：
```javascript
data: {
  doWhat: ''
},
methods: {
  showUp(event) {
    this.doWhat = `up ${event.target.tagName}`;
  },
  showDown(event) {
    this.doWhat = `down ${event.target.tagName}`;
  }
}
```
```html
<button 
  v-on:mousedown="showDown"
  v-on:mouseup="showUp">
  click me
</button>
<p>{{ doWhat }}</p>
```
或是使用物件綁定事件的方式：
```html
<button v-on="{ mousedown: showDown, mouseup: showUp }">click me</button>
<p>{{ doWhat }}</p>
```

使用物件綁定事件的方式，有以下幾點要注意：
- 指令沒參數，不能使用簡寫 `@`；
- 也無法使用修飾符號；
- 函式無法傳參數。

**CodePen Demo**：[Vue2.x - v-on 物件綁定事件](https://codepen.io/CHUPAIWANG/pen/ZEERPvM)

### 4. 事件物件

如果要獲取 `event` 物件，可以使用特殊變數 `$event`：
```html
<button v-on:click="alert($event.target)">event</button>
```

使用 `v-on` 綁定方法，預設會傳入一個參數 `event` 事件物件：
```html
<button v-on:click="addCount">event</button>
```
```javascript
methods: {
  addCount(event) {
    this.count += 1;
    alert(event.target);
  },
}
```

但是，直接呼叫事件處理函式，要訪問 `event` 物件，要自己將 `$event` 傳入：
```html
<button v-on:click="addCount(3, $event)">點我</button>
<p>{{ count }}</p>
```
```javascript
data: {
  count: 0,
},
methods: {
  addCount(i, event) {
    this.count += i;
    alert(event.target);
  },
}
```

## 事件行為修飾符號

### 1. 通用修飾符號

- `.stop`：呼叫 `event.stopPropagation()`，停止事件的繼續傳遞。
- `.prevent`：呼叫 `event.preventDefault()`，停止事件預設行為。
- `.self`：只有當事件是從監聽器所綁定的元素本身（非子元素）才呼叫事件處理函式，簡單來說就是事件處於目標階段才會呼叫事件處理函式。
- `.capture`：事件監聽器使用補獲模式。
- `.once`：該事件處理函式指被呼叫一次（還能被用到自定義的元件事件上）。
- `.passive`：與 `.prevent` 剛好相反，表示事件處理函式永遠不會呼叫 `preventDefault()`（所以別與 `.prevent` 共用）。
- `.native`：監聽根元素的原生事件（在元件上使用 `v-on` 只會監聽自定義事件，如果要監聽監聽根元素的原生事件時，就可以使用）。

使用 `.stop` 等同：
```javascript
// 使用 .stop 等同
doThis(event) {
  event.stopPropagation();
}

// 使用 .prevent 等同
doThis(event) {
  event.preventDefault();
}

// 使用 .self 等同
doThis(event) {
  if(!event.target === event.currentTarget) { return }
  // or
  if(!event.eventPhase === 2) { return }
}
```

`.capture`、`.once`、`.passive` 分別對應到 [`addEventListener()`](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener) 的 `options` 選項參數。`.native` 會在元件用到。

{{< notice success >}} 
一個指令可以加上複數修飾符號，但順序會影響執行的結果。
{{< /notice >}}

舉例來說，`v-on:click.prevent.self` 會阻止所有的點擊，而 `v-on:click.self.prevent` 只會阻止對元素自身的點擊。



### 2. 鍵盤事件修飾符號

鍵盤事件 `kepdown` 與 `keyup` 可以使用鍵盤事件修飾符號。

原本需要使用鍵盤事件的 [`key`](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key) 屬性來判斷是否按下 Enter 鍵來執行處理函式：
```javascript
submit(event) {
  if(event.key !=== 'Enter') {return}
  //...
}
```
現在只需要使用修飾符號：
```html
<div v-on:keyup.enter="submit"></div>
```

原則上，**[所有 `key` 值](https://developer.mozilla.org/zh-CN/docs/Web/API/KeyboardEvent/key/Key_Values)** 都有對應的修飾符號，若遇到 `PageDown` 這類名稱，需轉成 kebab-case 來使用：
```html
<input v-on:keyup.page-down="onPageDown">
```

#### 2.1 keyCode

{{< notice error >}} 
鍵盤事件的 [`keyCode`](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/keyCode) 屬性用法已經被廢棄了，雖然目前因為舊程式碼仍保留支援，但為了未來瀏覽器，盡可能不使用它。
{{< /notice >}}

使用 `keyCode` 修飾符號：
```html
<input v-on:keyup.13="submit">
```

為了在必要的情況下支持舊瀏覽器，Vue 提供了絕大多數常用的按鍵別名：
- `.enter` Enter 鍵
- `.tab` Tab 鍵
- `.delete`（包含 Backspace 鍵、Delete 鍵）
- `.esc` ESC 鍵
- `.space` 空白鍵
- `.up` 方向鍵上
- `.down` 方向鍵下
- `.left` 方向鍵左
- `.right` 方向鍵右

舉例來說，IE9 上面的某些按鍵 `key` 值會跟別人不同，因此使用上面按鍵的別名會是首要選擇。

另外可透過全域 [`config.keyCodes`](https://cn.vuejs.org/v2/api/#keyCodes) 物件，自定義按鍵修飾符號別名。

### 3. 系統鍵修飾符號

系統鍵修飾符號不只鍵盤事件能用，包含了滑鼠事件：
- `.ctrl`（MAC 沒有）
- `.alt`
- `.shift`
- `.meta`（MAC 的 command 鍵 `⌘`，Windows 則對應到 win 鍵 `⊞`）

```html
<!-- Alt + C -->
<input v-on:keyup.alt.67="clear">

<!-- Ctrl + Click -->
<div v-on:click.ctrl="doSomething">Do something</div>
```

{{< notice warning >}} 
請注意，系統鍵修飾符號和 `keyup` 事件一起使用時，系統鍵修飾符號必須處於按下狀態。也就是說，只有按住系統鍵修飾符號情況下釋放其他按鍵才能觸發。若想要單獨使用系統鍵和 `keyup` 事件，請改用 `keyCode` 對應的修飾符號，以 `keyup.ctrl` 來說，就是改用 `keyup.17`。
{{< /notice >}}

#### 3.1 精確判斷

`.exact` 修飾符號允許你控制由精確的系統修飾符號組合觸發的事件。

```html
<!-- 即使 Alt 或 Shift 被一同按下時也會觸發 -->
<button @click.ctrl="onClick">A</button>

<!-- 有且只有 Ctrl 單獨被按下的時候才觸發 -->
<button @click.ctrl.exact="onCtrlClick">A</button>

<!-- 沒有任何系統修飾符號被按下的時候才觸發 -->
<button @click.exact="onClick">A</button>
```

### 4. 滑鼠按鍵修飾符號：

- `.left`：只當點擊滑鼠左鍵時觸發。
- `.right`：只當點擊滑鼠右鍵時觸發。
- `.middle`：只當點擊滑鼠中鍵時觸發。

右鍵會跳出選單，可以搭配 `.prevent` 修飾符號。

## 視窗監聽

若是要監聽 `window` 物件，就沒辦法使用 `v-on` 指令了。

這時事件監聽就需要用到生命週期 `mounted`、`beforeDestroy` 鉤子。

在 `mounted` 鉤子上進行監聽：
```javascript
mounted() {
  window.addEventListener('resize', this.resizeHandler);
},
methods: {
  resizeHandler(event) {
    // do something
  },
},
```

但是這樣子的作法，若生命週期結束實體銷毀時，它並不會自動移除事件監聽，因此需要在 `beforeDestroy` 鉤子手動移除監聽：
```javascript
beforeDestroy() {
  window.removeEventListener('resize', this.resizeHandler);
},
```

## 事件委派

**事件委派（Event Delegation）** 或稱 **事件委託**、**事件代理**，是一種減少事件監聽的優化方式。利用了事件冒泡，將事件綁定在父元素上，再判斷當前觸發事件的元素是否是我們要的元素。

那麼在 Vue 中，使用 `v-for` 時，每個元素綁定事件時，是否有需要使用 **事件委派** 呢？

直接說結論：
1. 原則上沒必要使用，因為效能影響極小；
2. 除非要渲染數千甚至上萬的元素，才會嘗試使用事件委派。

詳細可以參考 **[第 94 題：vue 在 v-for 時給每項元素綁定事件需要用事件代理嗎？為什麼？](https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/145)**
