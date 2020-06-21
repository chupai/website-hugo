---
title: "Vue筆記 - Transition"
date: 2020-03-07
description: "這週是 六角鼠年鐵人賽 第五週，紀錄一下 Vue Transition 用法。"
keywords: ["Vue Transition", "Vue", "w3HexSchool","竹白"]
tags: ["Vue Transition", "Vue", "w3HexSchool"]
categories: ["w3HexSchool六角鼠年鐵人賽", "Vue筆記"]
draft: false
---

這週是 **六角鼠年鐵人賽** 第五週，紀錄一下 Vue Transition 用法。

<!--more-->

## 前言

在日常開發中，網頁動態效果是必不可少的一部分，不僅能讓元素直接的切換顯得更加自然，同時也能極大的增強用戶體驗。

雖然我們可以使用原生 CSS 或 JS 來實現動態效果，但 Vue 提供了更簡單和高效的方式。

### 1. Vue 的轉場工具

Vue 在插入、更新或者移除 DOM 時，提供多種不同方式的應用轉場效果。包括以下工具：
- 在 CSS `transition` 和 `animation` 中自動應用 `class` 名稱；
- 也可以配合使用第三方 CSS 動畫庫，例如 Animate.css。
- 在轉場鉤子函式中使用 JavaScript 直接操作 DOM；
- 也可以配合使用第三方 JavaScript 動畫庫，例如 Velocity.js。

## transition 元件

Vue 提供了 `transition` 的封裝元件，在下列情形中，可以給任何元素和元件添加進入/離開的轉場效果：
- 元素或元件初始渲染時
- 元素或元件顯示/隱藏時（使用 `v-if` 或 `v-show`）
- 元素或元件切換時

先看一個簡單的基本範例：
```html
<button v-on:click="show = !show">Toggle</button>
<transition>
  <div class="box" v-if="show">2</div>
</transition>
```
```javascript
data: {
  show: true,
},
```
```css
.v-enter, .v-leave-to {
  opacity: 0;
}
.v-leave, .v-enter-to {
  opacity: 1;
}

.v-enter-active,
.v-leave-active {
  transition: opacity 1s;
}
```

當我們使用 `transition` 元件將元素包住，Vue 會做以下處理：
1. 自動偵測目標元素是否應用 CSS `transition/animation` 如果是，在恰當的時機添加/刪除 CSS 類別名稱。
2. 如果 `transition` 元件提供了 JavaScript 鉤子函式，這些鉤子函式將在恰當的時機被呼叫。
3. 如果沒有找到 JavaScript 鉤子並且也沒有檢測到 CSS `transition/animation`，DOM 操作 (插入/刪除) 在下一幀中立即執行。

<iframe height="260" style="width: 100%;" scrolling="no" title="Vue CSS 轉場 基本範例" src="https://codepen.io/CHUPAIWANG/embed/eYmzyQN?height=265&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/eYmzyQN'>Vue CSS 轉場 基本範例</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 1. CSS 轉場的類別名稱

`transition` 元件共提供了，六種類別名稱提供切換。

![](https://i.imgur.com/mkKyGHV.png)

進場：
- `v-enter`：定義進場的開始狀態。
  - 在元素被插入之前生效，插入之後的下一幀被移除
- `v-enter-active`：定義進場生效時的狀態，在整個進場的階段中應用。
  - 在元素被插入之前生效，在 `transition/animation` 完成之後移除
  - 可用來定義進場的過程時間，延遲和曲線函數
- `v-enter-to`：**2.1.8版及以上** 定義進場的結束狀態。
  - 在元素被插入之後下一幀生效（與此同時 `v-enter` 被移除），在 `transition/animation` 完成之後移除

離場：
- `v-leave`：定義離場的開始狀態。
  - 在離場被觸發時立刻生效，下一幀被移除
- `v-leave-active`：定義離場生效時的狀態，在整個離場的階段中應用。
  - 在離場被觸發時立刻生效，在 `transition/animation` 完成之後移除
  - 這個類可以被用來定義離場的過程時間，延遲和曲線函數
- `v-leave-to`：**2.1.8版及以上** 定義離場的結束狀態。
  - 在離開轉場被觸發之後下一幀生效（與此同時 `v-leave` 被移除），在 `transition/animation` 完成之後移除

通常會設定 `v-enter` 與 `v-leave-active` 相同的效果、`v-leave` 與 `v-enter-to` 相同的效果，達到同尾相接形成一個循環，而 `v-enter-to` 與 `v-leave-to` 會用來設定轉場時間或動畫。

{{< alert theme="info" >}}
`enter-to` 預設 `opacity: 1`、`transform: none`，所以不一定要設定。
{{< /alert >}}


#### 1.1 CSS `transition`

`transition` 元件預設使用上述 `v-` 前綴的類別名稱。請參考一開始基本範例。

因為不可能只用一種動態效果，因此使用自訂義的前綴，需要加上 `name` 特性，舉例來說，`<transition name="fade">`，套用的那麼類別名稱的前綴就會是 `fade-enter`。

```html
<button v-on:click="show = !show">
  Toggle
</button>
<transition name="fade">
  <div class="box" v-if="show"></div>
</transition>
```

設定一個進場由透明度 0 變 1 並由右測 `100px` 處滑入，並且進場、離場分別設定不同的曲線函數：

```css
.fade-enter,
.fade-leave-to {
  opacity: 0;
  transform: translateX(100px);
}
.fade-leave, .fade-enter-to {
  opacity: 1;
}

.fade-enter-active {
  transition: all 0.3s ease;
}
.fade-leave-active {
  transition: all 0.8s cubic-bezier(1, 0.5, 0.8, 1);
}
```

<iframe height="265" style="width: 100%;" scrolling="no" title="Vue CSS 轉場 轉場範例" src="https://codepen.io/CHUPAIWANG/embed/MWYeZvd?height=265&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/MWYeZvd'>Vue CSS 轉場 轉場範例</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

再舉一個例子，由下往上進場，由右出場：
```css
.fade-enter {
  opacity: 0;
  transform: translateY(100%);
}

.fade-enter-to, .fade-leave {
  opacity: 1;
}

.fade-leave-to {
  opacity: 0;
  transform: translateX(100%);
}

.fade-enter-active,.fade-leave-active {
  transition: all 0.8s ease;
}
```
<iframe height="265" style="width: 100%;" scrolling="no" title="Vue CSS 轉場 轉場範例2" src="https://codepen.io/CHUPAIWANG/embed/zYGzQXB?height=265&theme-id=dark&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/zYGzQXB'>Vue CSS 轉場 轉場範例2</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

#### 1.2 CSS `animation`

CSS `animation` 用法同 CSS `transition`，區別是在動畫中 `v-enter` 類名在節點插入 DOM 後不會立即刪除，而是在 `animationend` 事件觸發時刪除。

```css
.bounce-enter-active {
  animation: bounce-in 0.5s;
}
.bounce-leave-active {
  animation: bounce-in 0.5s reverse;
}

@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
}
```

<iframe height="265" style="width: 100%;" scrolling="no" title="Vue CSS 轉場 動畫範例" src="https://codepen.io/CHUPAIWANG/embed/xxbOmjV?height=265&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/xxbOmjV'>Vue CSS 轉場 動畫範例</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 2. 使用自訂義類別名稱

我們可以通過以下 `transition` 元件特性來使用自訂義類別名稱：
- `enter-class`
- `enter-active-class`
- `enter-to-class` **2.1.8+**
- `leave-class`
- `leave-active-class`
- `leave-to-class` **2.1.8+**

他們的優先級高於普通的類別名稱。

舉個簡單得範例，來說明使用方式：
```css
.opacity-0 {
  opacity: 0;
}

.trs-time-3 {
  transition-duration: 3s;
}
```
```html
<transition
  enter-class="opacity-0"
  leave-to-class="opacity-0"
  enter-active-class="trs-time-3"
  leave-active-class="trs-time-3"
>
  <div class="box" v-if="show"></div>
</transition>
```

<iframe height="265" style="width: 100%;" scrolling="no" title="Vue CSS 轉場  自訂義 class" src="https://codepen.io/CHUPAIWANG/embed/JjoRGpG?height=265&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/JjoRGpG'>Vue CSS 轉場  自訂義 class</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

#### 2.1 第三方 CSS 動畫庫

自訂義類別名稱，對於套用第三方 CSS 動畫庫結合非常方便。

舉例來說，使用 [Animate.css](https://daneden.github.io/animate.css/) 的動畫效果：
```html
<button v-on:click="show = !show">
  Toggle
</button>
<transition
  enter-active-class="animated tada"
  leave-active-class="animated bounceOutRight"
>
  <div class="box" v-if="show"></div>
</transition>
<transition
  enter-active-class="animated fadeInUp"
  leave-active-class="animated flipInY"
>
  <div class="box" v-if="show"></div>
</transition>
```
<iframe height="500" style="width: 100%;" scrolling="no" title="Vue CSS 轉場  Animate.css" src="https://codepen.io/CHUPAIWANG/embed/dyPpYJB?height=265&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/dyPpYJB'>Vue CSS 轉場  Animate.css</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


或者是 [magic.css](https://www.minimamente.com/project/magic/)：

```html
<button v-on:click="show = !show">
  Toggle
</button>
<transition
  enter-active-class="magictime foolishIn"
  leave-active-class="magictime tinDownOut"
>
  <div class="box" v-if="show"></div>
</transition>
```
<iframe height="500" style="width: 100%;" scrolling="no" title="Vue CSS 轉場  magic" src="https://codepen.io/CHUPAIWANG/embed/NWPRGYj?height=265&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/NWPRGYj'>Vue CSS 轉場  magic</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 3. 轉場時間

#### 3.1 同時使用 `transition/animation`

Vue 為了知道轉場的完成，必須設置相應的事件監聽器。它可以是 `transitionend` 或 `animationend`，這取決於給元素應用的 CSS 規則。如果你使用其中任何一種，Vue 能自動識別類型並設置監聽。

但有時候會遇到需要同時設置的情況，但兩邊的完成時間不一致，這時候就需要手動設置 `transitionend` 或 `animationend` 來明確告知 Vue 要監聽的類型。

舉例來說，假設你分別使用自訂義的類別名稱和 Animate.css ：
```css
.fade-enter,.fade-leave-to {
  opacity: 0;
}

.fade-enter-to, .fade-leave {
  opacity: 1;
}

.fade-enter-active,.fade-leave-active {
  transition: opacity 3s;
}
```
```html
<transition
  name="fade"
  type="transition"
  enter-active-class="animated swing fade-enter-active"
  leave-active-class="animated bounce fade-leave-active"
>
  <div class="box" v-if="show"></div>
</transition>
```
這裡要注意，使用自訂義類別名稱優先度大於普通的類別名稱，所以`fade-enter-active` 和 `fade-leave-active` 記得要補上。

Animate.css 預設動畫時間為 1s，而我們設置的 `transition` 時間為 3s，Animate.css 的動畫很外就觸發完成了，而轉場還沒結束。在很多情況下，Vue 可以自動得出轉場效果的完成時機，但還是有可能出現例外。

為了避免出現例外，你就需要手動設定 `type="transition"` 以 `transition` 的時間為準。反之動畫時間大於轉場就以動畫時間為準。

<iframe height="265" style="width: 100%;" scrolling="no" title="Vue CSS 轉場  同时使用轉場和動畫" src="https://codepen.io/CHUPAIWANG/embed/BayLoXZ?height=265&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/BayLoXZ'>Vue CSS 轉場  同时使用轉場和動畫</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

#### 3.2 顯性的轉場持續時間

上面已經提過了，在很多情況下，Vue 可以自動得出轉場效果的完成時機。預設情況下，Vue 會等待其在轉場效果的根元素的第一個 `transitionend` 或 `animationend` 事件。

但在某些情況下，還可以自定義整個動態效果總時間長，使用的是 `duration` 特性搭配 `v-bind`。

舉例來說，總時常為 5s：
```html
<transition :duration="5000">...</transition>
```
也可以分別設定進入和移除持續時間：
```html
<transition :duration="{ enter: 500, leave: 800 }">...</transition>
```

這是一個 `transition` 需要 5秒，但總轉場時間強制設為 1秒的範例：
<iframe height="265" style="width: 100%;" scrolling="no" title="Vue CSS 轉場  顯性的轉場持續時間" src="https://codepen.io/CHUPAIWANG/embed/mdJwNwe?height=265&theme-id=dark&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/mdJwNwe'>Vue CSS 轉場  顯性的轉場持續時間</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 4.初始渲染的轉場

透過設定 `appear` 特性，設置節點在初始渲染的轉場。

```html
<transition appear>
  <!-- ... -->
</transition>
```
>[CodePen](https://codepen.io/CHUPAIWANG/full/dyPpJKO)，載入畫面時，會有進場效果。

`appear` 預設使用進場的類別，如果要與進場套用不同的動畫效果，需要使用自定義的類別名稱：
```html
<transition appear>
  <!-- ... -->
</transition>
```
>[CodePen](https://codepen.io/CHUPAIWANG/full/dyPpJKO)，載入畫面時，會有進場效果。

`appear` 預設使用進場的類別，如果要與進場套用不同的動畫效果，需要使用自定義的類別名稱：
```html
<transition
  appear
  appear-class="custom-appear-class"
  appear-to-class="custom-appear-to-class" (2.1.8+)
  appear-active-class="custom-appear-active-class"
>
  <!-- ... -->
</transition>
```

## 多個元素的轉場

`<transition>` 也可以用在多個元素的轉場。

舉例來說：
```html
<transition>
  <table v-if="items.length > 0">
    <!-- ... -->
  </table>
  <p v-else>Sorry, no items found.</p>
</transition>
```


但要注意，Vue 在切換畫面時，並不會完全置換元素，會使用一種最大限度減少動態元素並且儘可能的嘗試修復/再利用相同類型元素的算法。

因此當有相同標籤的元素在做切換時，需要使用 `key` 特性設置唯一的值來標記以讓 Vue 區分它們。

```html
<transition>
  <button v-if="isEditing" key="save">
    Save
  </button>
  <button v-else key="edit">
    Edit
  </button>
</transition>
```

在一些場景中，也可以通過給同一個元素的 key 特性設置不同的狀態來代替 `v-if` 和 `v-else`，上面的範例可以重寫為：
```html
<transition>
  <button v-bind:key="isEditing">
    {{ isEditing ? 'Save' : 'Edit' }}
  </button>
</transition>
```

使用多個 `v-if` 的多個元素的轉場可以重寫為綁定了動態屬性的單個元素轉場。例如：
```html
<transition>
  <button v-if="docState === 'saved'" key="saved">
    Edit
  </button>
  <button v-if="docState === 'edited'" key="edited">
    Save
  </button>
  <button v-if="docState === 'editing'" key="editing">
    Cancel
  </button>
</transition>
```
可以重寫為：
```html
<!-- .html -->
<transition>
  <button :key="docState">
    {{ buttonMessage }}
  </button>
</transition>
```
```javascript
// ...
computed: {
  buttonMessage: function () {
    switch (this.docState) {
      case 'saved': return 'Edit'
      case 'edited': return 'Save'
      case 'editing': return 'Cancel'
    }
  }
}
```

### 1. 轉場模式

請考慮以下程式碼：
```html
<transition>
  <button :key="isEditing" @click="isEditing = !isEditing">
    {{ isEditing ? 'On' : 'Off' }}
  </button>
</transition>
```

<iframe height="200" style="width: 100%;" scrolling="no" title="Vue CSS 轉場  轉場模式 問題" src="https://codepen.io/CHUPAIWANG/embed/ZEYpreQ?height=265&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/ZEYpreQ'>Vue CSS 轉場  轉場模式 問題</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

在 on 按鈕和 off 按鈕的轉場中，兩個按鈕都被重繪了，一個離開轉場的時候另一個開始進入轉場。這是 `<transition>` 的預設行為，進入和離開同時發生。

你可能會想到，可以加上 `position: absolute`，因為兩者重疊了，所以不會產生元素位移的情況：
```css
button {
  position: absolute;
}
```
<iframe height="200" style="width: 100%;" scrolling="no" title="Vue CSS 轉場  轉場模式 絕對定位" src="https://codepen.io/CHUPAIWANG/embed/zYxKRzo?height=265&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/zYxKRzo'>Vue CSS 轉場  轉場模式 絕對定位</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

但如果加上 `translate` 讓它們運動像滑動，還是會有兩個元素同時出現的破綻：
```css
.v-enter,
.v-leave-to {
  opacity: 0;
  transform: translateX(100%);
}
```

<iframe height="200" style="width: 100%;" scrolling="no" title="Vue CSS 轉場  轉場模式 絕對定位 + 滑動" src="https://codepen.io/CHUPAIWANG/embed/abzmqyW?height=265&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/abzmqyW'>Vue CSS 轉場  轉場模式 絕對定位 + 滑動</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

同時生效的進入和離開的轉場不能滿足所有要求，所以 Vue 提供了轉場模式：
- `in-out`：新元素先進行轉場，完成之後當前元素轉場離開。
- `out-in`：當前元素先進行轉場，完成之後新元素轉場進入。

加上 `mode="out-in"` 的效果：
```html
<transition mode="out-in">
  <button :key="isEditing" @click="isEditing = !isEditing">
    {{ isEditing ? 'On' : 'Off' }}
  </button>
</transition>
```
```css
.v-enter-active, .v-leave-active {
  transition: 1.5s opacity;
}
```
<iframe height="265" style="width: 100%;" scrolling="no" title="Vue CSS 轉場  轉場模式 out-in" src="https://codepen.io/CHUPAIWANG/embed/vYEXdWr?height=265&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/vYEXdWr'>Vue CSS 轉場  轉場模式 out-in</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

`in-out` 模式的範例：
```css
button {
  position: absolute;
}

.v-enter,.v-leave-to {
opacity: 0;
}

.v-enter {
  transform: translateX(100%);
}

.v-leave-to {
  transform: translateX(-100%);
}
```
<iframe height="265" style="width: 100%;" scrolling="no" title="Vue CSS 轉場  轉場模式 in-out" src="https://codepen.io/CHUPAIWANG/embed/OJPRvVJ?height=265&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/OJPRvVJ'>Vue CSS 轉場  轉場模式 in-out</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## 多個元件的轉場

多個元件的轉場簡單很多，我們不需要使用 `key` 特性。只需要使用 **動態切換** 元件：

```html
<!-- .html -->
<div id="app">
  <input type="radio" value="v-a" v-model="view" />A
  <input type="radio" value="v-b" v-model="view" />B
  <transition name="component-fade" mode="out-in">
    <component v-bind:is="view"></component>
  </transition>
</div>
```
```css
.component-fade-enter,
.component-fade-leave-to {
  opacity: 0;
}

.component-fade-enter-active,
.component-fade-leave-active {
  transition: opacity 0.3s ease;
}

```
```javascript
const vm = new Vue({
  el: '#app',
  data: {
    view: 'v-a',
  },
  components: {
    'v-a': {
      template: '<div>Component A</div>',
    },
    'v-b': {
      template: '<div>Component B</div>',
    },
  },
});
```

## transition-group 元件

### 1. 大量元素的轉場

目前為止，都是針對單個節點，或是同一時間渲染多個節點中的一個。如果要針對由 `v-for` 產生的大量元素（例如：列表），就要改為使用 transition-group 元件。

transition-group 元件有以下幾個特點：
- 不同於 `<transition>`，它會以一個真實元素呈現：預設為一個 `<span>`。可以通過 `tag` 特性更換為其他元素。
- 轉場模式（`in-out` 與 `out-in`）不可用，因為我們不再相互切換特有的元素。
- 內部元素 **總是需要** 提供唯一的 `key` 屬性值。
- CSS 轉場的類將會應用在內部的元素中，而不是這個組/容器本身。

#### 1.1 進入/離開的轉場

首先我們看一段程式碼：
```html
<button v-on:click="add">Add</button>
<button v-on:click="remove">Remove</button>

<ul>
  <li v-for="item in items" :key="item" class="list-item">
    {{ item }}
</ul>
```
按下 Add 會隨機插入數字，按下 Remove 會隨機移除數字。
<iframe height="265" style="width: 100%;" scrolling="no" title="Vue 大量元素的轉場 " src="https://codepen.io/CHUPAIWANG/embed/povELjR?height=265&theme-id=dark&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/povELjR'>Vue 大量元素的轉場 </a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

接下來我們改用 transition-group 元件，增加轉場效果。

transition-group 元件預設會有一個 `<span>` 的根元素，它會包住由 `v-for` 產生的多個元素當成其子元素。


我們透過 `tag` 特性，將其改成 `<ul>`：
```html
<transition-group name="list" tag="ul">
  <li v-for="item in items" v-bind:key="item" class="list-item">
    {{ item }}
  </li>
</transition-group>
```
並加上 CSS：
```css
.list-enter,
.list-leave-to {
  opacity: 0;
  transform: translateY(100%);
}

.list-leave,
.list-enter-to {
  opacity: 1;
}

.list-enter-active,
.list-leave-active {
  transition: all 1s;
}
```
<iframe height="265" style="width: 100%;" scrolling="no" title="Vue 大量元素的轉場 - transition-group" src="https://codepen.io/CHUPAIWANG/embed/RwPZgwM?height=265&theme-id=dark&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/RwPZgwM'>Vue 大量元素的轉場 - transition-group</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

不過這個範例有一個明顯問題，當新增和移除元素的時候，周圍的元素會瞬間移動到他們的新佈局的位置，這會顯得不自然，我們將會在之後解決這個問題。

#### 1.2 排序轉場

transition-group 元件還有一個特殊之處。不僅可以進入和離開動畫，還可以改變定位。

`v-move` 特性，它會在元素的改變定位的過程中應用。像之前的列別名稱一樣，可以通過 `name` 屬性來自定義前綴，也可以通過 `move-class` 屬性手動設置。

`v-move` 對於設置轉場的切換時機和轉場曲線非常有用。

```html
<button v-on:click="items.reverse()">Reverses</button>

<transition-group name="flip-list" tag="ul">
  <li 
  v-for="item in items"
  :key="item"
  class="list-item">{{ item }}
  </li>
</transition-group>
```
```css
.flip-list-move {
  transition: transform 1s;
}
```
```javascript
const vm = new Vue({
  el: '#app',
  data: {
    items: [1, 2, 3, 4, 5, 6, 7, 8, 9],
  },
});
```
<iframe height="400" style="width: 100%;" scrolling="no" title="Vue 大量元素的轉場 - v-move" src="https://codepen.io/CHUPAIWANG/embed/bGNwzwz?height=265&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/bGNwzwz'>Vue 大量元素的轉場 - v-move</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

Vue 使用了一個叫 [FLIP](https://aerotwist.com/blog/flip-your-animations/) 簡單的動畫隊列。使用 `transforms` 將元素從之前的位置平滑轉場新的位置。

讓我們修正上節不自然的的程式碼：
```css
.list-leave-active {
  position: absolute;
}

.list-move {
  transition: 1s;
}
```
<iframe height="265" style="width: 100%;" scrolling="no" title="Vue 大量元素的轉場 - 進入/離開的轉場 v-move" src="https://codepen.io/CHUPAIWANG/embed/KKwgJNO?height=265&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/KKwgJNO'>Vue 大量元素的轉場 - 進入/離開的轉場 v-move</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

{{< alert theme="danger" >}}
另外要注意，FLIP 轉場的元素不能設置為 `display: inline`。作為替代方案，可以設置為 `display: inline-block`、`display: flex-block` 或者放置於 FlexBox 父容器中
{{< /alert >}}

<iframe height="500" style="width: 100%;" scrolling="no" title="Vue 大量元素的轉場 - 打亂" src="https://codepen.io/CHUPAIWANG/embed/VwLzMxp?height=265&theme-id=dark&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/VwLzMxp'>Vue 大量元素的轉場 - 打亂</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## JavaScript 鉤子

`transition/animation` 的核心概念，就是在對應的時間點新增、移除類別。除了這種在被動的時間點加上類別的方式外，Vue 也提供對應時間的 JavaScript 鉤子讓我們在該時間點使用 JavaScript。

事件鉤子：
- `before-enter`：進場前啟動。
- `enter`：進場時啟動。
- `after-enter`：進場結束後啟動。
- `enter-cancelled`：在未完成進場時取消動作。
- `before-leave`：離場前啟動。
- `leave`：離場時啟動。
- `after-leave`：離場結束後啟動。
- `leave-cancelled`：在未完成離場時取消動作。（只用於 `v-show` 中）

```html
<transition
  v-on:before-enter="beforeEnter"
  v-on:enter="enter"
  v-on:after-enter="afterEnter"
  v-on:enter-cancelled="enterCancelled"

  v-on:before-leave="beforeLeave"
  v-on:leave="leave"
  v-on:after-leave="afterLeave"
  v-on:leave-cancelled="leaveCancelled"
>
</transition>
```
```javascript
methods: {
  beforeEnter: function(el) {},
  enter: function(el, done) {
    done();
  },
  afterEnter: function(el) {},
  enterCancelled: function(el) {},

  beforeLeave: function(el) {},
  leave: function(el, done) {
    done();
  },
  afterLeave: function(el) {},
  leaveCancelled: function(el) {},  
}
```
- 如果只要單獨使用 JS 鉤子轉場，可以設置 `v-bind:css="false"`，Vue 會跳過 CSS 的檢測，避免轉場過程受到 CSS 的影響。
- `enter` 和 `leave` 的回呼函式 `done` 是可選的：
  - 參數不注入 `done` 時，預設為同步呼叫，會自動呼叫 `done()`。
  - 如果有 `done` 參數，就需要手動呼叫 `done()`，沒呼叫就不會進入　`after-enter`／`after-leave`。
  - 如果想要搭配 CSS 同步自動 call done 的話，就不要帶 done 參數
  - 只使用 JavaScript 鉤子的時候，要記得呼叫 `done()`。

初始渲染也有事件鉤子：
- `before-appear`：載入前。
- `appear`：載入時。
- `after-appear`：載入後。
- `appear-cancelled`：載入開始後，執行取消載入。

## 路由轉場

`<router-view>` 一樣可以使用 `<transition>` 元件：
```html
<transition>
  <router-view></router-view>
</transition>
```

### 1. 單個路由的轉場

如果要讓每個路由元件有各自的轉場效果，可以在元件內使用 `<transition>` 元件並設置不同的 `name`。

```html
<!-- Views1.vue -->
<template >
  <transition name="slide">
    ...
  </transition>
</template>
```
```html
<!-- Views2.vue -->
<template >
  <transition name="fade">
    ...
  </transition>
</template>
```

### 2. 動態轉場

```html
<transition :name="transitionName">
  <router-view></router-view>
</transition>
```

在接著在父元件內，監聽 `$route` 決定使用哪種轉場效果：
```javascript
watch: {
  '$route' (to, from) {
    const toDepth = to.path.split('/').length
    const fromDepth = from.path.split('/').length
    this.transitionName = toDepth < fromDepth ? 'slide-right' : 'slide-left'
  }
}
```

### 3. Vueg

>[Vueg](https://github.com/jaweii/Vueg----page-transition-plugin)
為 vue-router 添加轉場效果的開源 Vue 套件。

## 交錯轉場

這裡有一段程式碼：
```html
<button @click="show = !show">show</button>
<ul v-if="show">
  <li v-for="n in num" :key="n">
    {{ n }}
  </li>
</ul>
```

```javascript
data: {
  num: 5,
  show: false,
},
```
<iframe height="265" style="width: 100%;" scrolling="no" title="交錯轉場 範例" src="https://codepen.io/CHUPAIWANG/embed/wvaPWYN?height=265&theme-id=dark&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/wvaPWYN'>交錯轉場 範例</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

我們要如何將它改成交錯轉場？

### 1. `transition-delay`

最簡單的方式，就是透過 `transition-delay` 來實現列表的交錯轉場。
```css
.custom-appear-active-class {
  transition: opacity 0.3s;
}

.custom-appear-active-class:nth-child(5n + 2) {
  transition-delay: 0.3s;
}
.custom-appear-active-class:nth-child(5n + 3) {
  transition-delay: 0.5s;
}
.custom-appear-active-class:nth-child(5n + 4) {
  transition-delay: 0.7s;
}
.custom-appear-active-class:nth-child(5n + 5) {
  transition-delay: 0.9s;
} 
```

但缺點也很明顯，要寫大量的 CSS，修改起來也不靈活。

<iframe height="400" style="width: 100%;" scrolling="no" title="交錯轉場 transition-delay" src="https://codepen.io/CHUPAIWANG/embed/vYEXbdr?height=265&theme-id=dark&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/vYEXbdr'>交錯轉場 transition-delay</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


### 2. 透過 `data` 屬性與 JavaScript 交流

透過 `data` 屬性取得元素的 `index`，所以補上 `:data-index = index`：
```html
  <transition-group tag="ul"
    v-if="show"
    v-bind:css="false"
    @appear="appear"
    appear >
    <li v-for="(n,index) in num" :key="n" :data-index = index >
      {{ n }}
    </li>
  </transition-group>
```

利用 `el.dataset` 取取得元素的 `data` 屬性。
```javascript
methods:{
  appear(el, done) {
    const index = el.dataset.index;  // 取得 index
    const delay = index * 0.3;
    gsap.from(el,{
      duration: 0.3,
      opacity: 0,
      delay: delay,
    })
    done();
  },
},
```
我這裡使用 [GSAP3 動畫](https://greensock.com/docs/v3/GSAP/Tween)來設置動畫。

<iframe height="265" style="width: 100%;" scrolling="no" title="交錯轉場  GSAP3" src="https://codepen.io/CHUPAIWANG/embed/MWwOvwy?height=265&theme-id=dark&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/MWwOvwy'>交錯轉場  GSAP3</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


或是使用 Velocity.js 搭配 `setTimeout`：
```javascript
methods:{
  beforeAppear(el) {
    el.style.opacity = 0;
  },
  appear(el, done) {
    const index = el.dataset.index;
    const delay = index * 300;
    setTimeout(function () {
      Velocity(
        el,
        { opacity: 1 },
        { complete: done }
      );
    }, delay);
  },
},
```
<iframe height="265" style="width: 100%;" scrolling="no" title="交錯轉場  Velocity" src="https://codepen.io/CHUPAIWANG/embed/eYNePJM?height=265&theme-id=dark&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/eYNePJM'>交錯轉場  Velocity</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>





