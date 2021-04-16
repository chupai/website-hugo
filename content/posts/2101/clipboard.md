---
title: "JavaScript 剪貼簿複製操作"
date: 2021-01-30
description: "剪貼簿複製操作。"
keywords: ["JavaScript", "剪貼簿", "複製"]
tags: ["JavaScript", "剪貼簿"]
categories: ["JavaScript筆記"]
image: images/covers/2101/clipboard.png
draft: false
---

>[JavaScript筆記 目錄](/posts/190620_javascript)

<!--more-->
{{< featuredImage >}}


## 前言

在瀏覽器中，將資料複製到剪貼簿的方法有三種：
1. [Document.execCommand()](https://developer.mozilla.org/zh-TW/docs/Web/API/Document/execCommand) 的 `copy` 指令
2. [Clipboard API ](https://developer.mozilla.org/zh-CN/docs/Web/API/Clipboard_API)
3. [ClipboardEvent](https://developer.mozilla.org/zh-CN/docs/Web/API/ClipboardEvent) 的 `copy`、`paste` 事件


遠古時期還有使用 Flash 來操作剪貼簿，這裡就不討論了。

## Document.execCommand()

Document.execCommand() 的 `copy` 指令能將選取到的內容複製到剪貼簿。

### 1. 複製操作

執行 `document.execCommand('copy')` 就可以將當前選取範圍複製到剪貼簿。

```javascript
const btnCopy = document.querySelector('.btnCopy');

btnCopy.addEventListener('click', function() {
  document.execCommand('copy');
});
```

若要顯示當前選取內容值，可以使用 [`window.getSelection()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/getSelection) 表示當前使用者選取的範圍或光標當前位置，回傳 Selection 物件，並使用 `+ ''` 或 `toString()` 轉字串。

```javascript
const btnCopy = document.querySelector('.btnCopy');

btnCopy.addEventListener('click', function() {
  document.execCommand('copy');
  const selection = window.getSelection();
  alert(selection.toString());
});
```

<iframe height="265" style="width: 100%;" scrolling="no" title="document.execCommand - 複製選取內容" src="https://codepen.io/CHUPAIWANG/embed/jOMedpY?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/jOMedpY'>document.execCommand - 複製選取內容</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 2. 複製輸入框內容

`document.execCommand('copy')` 的缺點就是只能複製選取的內容，如果我們要複製 `<textarea>` 或 `<input type="text">` 的內容值，可以使用 [`select()`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLInputElement/select) 選取內容值，再執行 `document.execCommand('copy')`。

```javascript
const btnCopy = document.querySelector('.btnCopy');

btnCopy.addEventListener('click', function() {
　const inputText = document.querySelector('.inputText');
  inputText.select();
  document.execCommand('copy');
});
```
<iframe height="265" style="width: 100%;" scrolling="no" title="document.execCommand - 複製輸入框內容" src="https://codepen.io/CHUPAIWANG/embed/gOwBQKe?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/gOwBQKe'>document.execCommand - 複製輸入框內容</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

若 `select()` 在 iOS 無反應，使用 `setSelectionRange` 即可：
```javascript
inputText.select();
inputText.setSelectionRange(0, element.value.length);
```
>[input.select() does not work on iOS](https://nicolasbouliane.com/blog/input-select-does-not-work-on-ios)

### 3. 複製指定元素文字

如果我們要複製某一個元素的文字內容，就需要建立選取範圍。 

```javascript
const btnCopy = document.querySelector('.btnCopy');

btnCopy.addEventListener('click', function() {
  // 建立 Range 物件
  const range = document.createRange();
  // 將指定元素內容加到 Range 中
  const texts = document.querySelector('.texts');
  range.selectNode(texts);
  // 取得 Selection 物件
  const selection = window.getSelection();
  // 先清空當前選取範圍
  selection.removeAllRanges();
  // 加入 Range 
  selection.addRange(range);
  
  document.execCommand('copy');
  selection.removeAllRanges();
});
```

<iframe height="265" style="width: 100%;" scrolling="no" title="document.execCommand - 複製指定元素文字" src="https://codepen.io/CHUPAIWANG/embed/vYXVPmm?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/vYXVPmm'>document.execCommand - 複製指定元素文字</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 4. 複製任意內容

如果要將任意內容複製到剪貼簿，需要先建立 `<textarea>` 元素，並將指定內容設為 `value`，再插入 DOM 樹中，呼叫 `select()` 與執行複製，最後刪除 `<textarea>` 元素。

```javascript
const btnCopy = document.querySelector('.btnCopy');

btnCopy.addEventListener('click', function() {
  const value = 'hello!';
  const el = document.createElement('textarea');
  el.value = value;
  document.body.appendChild(el);
  el.select();
  document.execCommand('copy');
  document.body.removeChild(el);
});
```
<iframe height="265" style="width: 100%;" scrolling="no" title="document.execCommand - 複製任意內容" src="https://codepen.io/CHUPAIWANG/embed/abmgXox?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/abmgXox'>document.execCommand - 複製任意內容</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>




### 優缺點

優點：
- 基本上支援所有主流瀏覽器。

[Document API: execCommand: copy - Can I use](https://caniuse.com/?search=Document.execCommand%20copy)
![](https://i.imgur.com/JBEbsaw.png)

缺點：
- 只能複製選取中的內容，複製任意內容需要建立臨時的 `<textarea>` 可能導致畫面閃爍，而且還會取消使用者當前選取的內容。
- 同步操作，若複製大量資料，可能導致頁面卡頓。


## Clipboard API

[Clipboard API](https://developer.mozilla.org/zh-CN/docs/Web/API/Clipboard_API) 是新一代的剪貼簿操作方法，被設計用來取代 `document.execCommand()`，能直接訪問剪貼簿，為非同步操作，所有操作方法都會回傳 Promise 物件。

### 1. Clipboard 物件

`navigator.clipboard` 會回傳 [Clipboard](https://developer.mozilla.org/zh-CN/docs/Web/API/Clipboard) 物件，所有操作都是透過它進行。

```javascript
const cb = navigator.clipboard;
```
若 `navigator.clipboard` 回傳 `undefined`，表示當前瀏覽器不支援。

### 2. 安全和權限

直接訪問剪貼簿的操作其實並不安全，因此 Clipboard API 僅支持通過 HTTPS 提供的頁面（開發環境 localhost 例外），且必須使用者授權才能訪問。

![](https://i.imgur.com/bynVkGu.png)


若是在 `<iframe>` 中運行，父頁面必須授予 `clipboard-read`（讀權限） 或 `clipboard-write`（寫權限）。

```javascript
<iframe allow="clipboard-read; clipboard-write"></iframe>
```

另外，我們可以使用 [Permissions API](https://developer.mozilla.org/zh-CN/docs/Web/API/Permissions) 的 [`Permissions.query()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Permissions_API/Using_the_Permissions_API) 查看是否有權訪問剪貼簿。


### 3. 操作方法

- `readText()`：讀取剪貼簿純文字內容。
- `writeText()`：對剪貼簿寫入純文字內容。
- `read()`：讀取剪貼簿複合內容。
- `write()`：對剪貼簿寫入複合內容。

以上方法皆會回傳一個 Promise 物件，讀取方法有結果值可以接收，若使用者拒絕授權，會拋出 NotAllowedError。

```javascript
// 讀
navigator.clipboard.readText()
  .then((text) => {
    console.log(text);
  })
  .catch((err) => {
    console.error(err);
  });

// 寫

const text = 'Hello!';
navigator.clipboard.writeText(text)
  .then(() => {
    console.log('內容已複製');
  })
  .catch((err) => {
    console.error(err);
  });
```

也可以使用 `async/await` 語法：
```javascript
async function readFromClipboard() {
  try {
    const text = await navigator.clipboard.readText();
    console.log(text);
  } catch (error) {
    console.error(error);
  }
}

async function writeToClipboard(text) {
  try {
    await navigator.clipboard.writeText(text);
    console.log('內容已複製');
  } catch (error) {
    console.error(error);
  }
}
```

### 優缺點

優點：
- 操作簡單，能直接訪問剪貼簿，不需要像 `document.execCommand('copy')` 需要有選取範圍才能使用。
- 非同步操作。

缺點：
- 需要使用者授權。
- 瀏覽器支援度不佳。

[Clipboard API: readText- Can I use](https://caniuse.com/mdn-api_clipboard_readtext)
![](https://i.imgur.com/aS0O6lI.png)

[Clipboard API: writeText- Can I use](https://caniuse.com/mdn-api_clipboard_writetext)
![](https://i.imgur.com/XcQ8LKl.png)

[Clipboard API: read- Can I use](https://caniuse.com/mdn-api_clipboard_read)
![](https://i.imgur.com/YNEACXZ.png)

[Clipboard API: write- Can I use](https://caniuse.com/mdn-api_clipboard_write)
![](https://i.imgur.com/He1bXsS.png)


## ClipboardEvent

[`ClipboardEvent`](https://developer.mozilla.org/zh-CN/docs/Web/API/ClipboardEvent) 介面描述了與修改剪貼簿相關的事件。當使用者執行剪貼簿相關的操作，我們可以透過監聽 `cut` 、`copy` 和 `paste`，攔截事件以進行操作。

`cut` 、`copy` 和 `paste` 這些事件僅在當前頁面上操作時觸發。

這些事件的 `event` 物件中的 [`clipboardData`](https://developer.mozilla.org/zh-CN/docs/Web/API/ClipboardEvent/clipboardData) 屬性 ，有三個方法可以使用：
- [`clearData()`](https://developer.mozilla.org/zh-CN/docs/Web/API/DataTransfer/clearData)：清除剪貼簿資料，可指定資料類型，若不指定則清除全部。
- [`getData()`](https://developer.mozilla.org/zh-CN/docs/Web/API/DataTransfer/getData)：取得剪貼簿資料，需要指定資料類型。
- [`setData()`](https://developer.mozilla.org/zh-CN/docs/Web/API/DataTransfer/setData)：修改剪貼簿資料，需要指定資料類型。

### 1. 複製操作

當我們執行複製操作時，可以監聽 `copy` 事件，將指定內容放入剪貼簿。

```javascript
document.addEventListener('copy', function(event) {
  event.preventDefault();
  event.clipboardData.setData('text/plain', 'Hello!');
});
```

我們有時候複製某些網站的內容時，會自動加上版權出處：
```javascript
document.addEventListener('copy', function(event) {
  const text = window.getSelection().toString();
  if (text.length > 15) {
    event.preventDefault();
    event.clipboardData.setData('text/plain', '來源：竹白記事本\n' + text);
  }
});
```
<iframe height="400" style="width: 100%;" scrolling="no" title="copy 事件" src="https://codepen.io/CHUPAIWANG/embed/eYdqRJY?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/eYdqRJY'>copy 事件</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


## Clipboard.js

[Clipboard.js](https://clipboardjs.com/) 是一個將文字複製到剪貼簿的 JavaScript 函式庫，操作簡單、輕量、不依賴其他框架，依賴 [Selection API](https://developer.mozilla.org/en-US/docs/Web/API/Selection) 和 [execCommand API](https://developer.mozilla.org/en-US/docs/Web/API/Document/execCommand) 支持主流瀏覽器。


安裝：
```
# NPM 管理
npm install clipboard --save

# 引入 CDN
https://cdnjs.cloudflare.com/ajax/libs/clipboard.js/2.0.6/clipboard.min.js
```

### 1. 基本用法

建立實體，傳入要綁定的元素：
```javascript
const clipboard = new ClipboardJS('.btn');
```
可以是 **DOM 選擇器** 或 **HTML 元素** 或 **HTML 元素陣列**。

首先，若要複製當前的內容，可以使用 `data-clipboard-text` 設定內容值：　
```html
<button class="btn" data-clipboard-text="hello!">copy</button>
```

<iframe height="200" style="width: 100%;" scrolling="no" title="Clipboard.js - 基本用法" src="https://codepen.io/CHUPAIWANG/embed/yLVBwor?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/yLVBwor'>Clipboard.js - 基本用法</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

如果要從其他元素取值，可以透過 `data-clipboard-target` 指定目標元素：
```html
<input id="foo" type="text" value="https://chupai.github.io/">
<button class="btn" data-clipboard-target="#foo">copy</button> 
```
目標為 `<input>` 或 `<textarea>` 可以複製內容值，若是 其他元素，例如 `<div>` 則複製元素中的文字。

<iframe height="200" style="width: 100%;" scrolling="no" title="Clipboard.js - 從其他元素取值" src="https://codepen.io/CHUPAIWANG/embed/qBqWGqg?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/qBqWGqg'>Clipboard.js - 從其他元素取值</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

預設操作為 `copy` 複製，使用 `data-clipboard-action` 可以設置 `cut` 剪下。
```html
<input id="foo" type="text" value="https://chupai.github.io/">
<button class="btn" data-clipboard-target="#foo" data-clipboard-action="cut">cut</button>
```
但只對 `<input>` 和 `<textarea>` 有作用。

<iframe height="265" style="width: 100%;" scrolling="no" title="Clipboard.js - cut" src="https://codepen.io/CHUPAIWANG/embed/YzpKbLX?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/YzpKbLX'>Clipboard.js - cut</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 2. 事件處理

如果想要在剪貼簿操作完成後執行其他操作，可以使用 `success` 或 `error` 自定義事件：
```javascript
const clipboard = new ClipboardJS('.btn');

clipboard.on('success', function(e) {
  console.info('Action:', e.action);
  console.info('Text:', e.text);
  console.info('Trigger:', e.trigger);

  e.clearSelection();
});

clipboard.on('error', function(e) {
  console.error('Action:', e.action);
  console.error('Trigger:', e.trigger);
});
```

### 3. 進階用法

若不希望修改 HTML 程式碼（也就是使用 `data-`），建立實體時，還可以傳入一個選項物件。

`target` 選項為一個函式，需要回傳一個 Node，作為目標元素：
```javascript
const clipboard = new ClipboardJS('.btn', {
  target(trigger) {
    // trigger 參數為觸發元素 Node
    return document.querySelector('#foo');
  }
});
```
<iframe height="200" style="width: 100%;" scrolling="no" title="Clipboard.js - 進階用法" src="https://codepen.io/CHUPAIWANG/embed/ExNxmWp?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/ExNxmWp'>Clipboard.js - 進階用法</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

`text` 選項為一個函式，需要回傳一個字串，作為複製內容：
```javascript
const clipboard = new ClipboardJS('.btn', {
  text(trigger) {
    // trigger 參數為觸發元素 Node
    return 'Hello!';
  }
});
```
<iframe height="200" style="width: 100%;" scrolling="no" title="Clipboard.js - 進階用法  text" src="https://codepen.io/CHUPAIWANG/embed/dyOyWJB?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/dyOyWJB'>Clipboard.js - 進階用法  text</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

`container` 選項能傳入獲得焦點的元素：
```javascript
const clipboard = new ClipboardJS('.btn', {
  container: document.getElementById('modal'),
});
```

### 4. 清除

若使用 SPA 單頁應用程式，想更精確管理 DOM 生命週期，想清除事件、實體，可以使用 `destroy()`。
```javascript
const clipboard = new ClipboardJS('.btn');
clipboard.destroy();
```

## 參考文獻

- [Async Clipboard API: Accessing the clipboard using JavaScript](https://arnellebalane.com/blog/async-clipboard-api/)
- [Cut, Copy and Paste in JavaScript with the Clipboard API](https://www.sitepoint.com/clipboard-api/)
- [A better way to copy text to Clipboard in JavaScript](https://komsciguy.com/js/a-better-way-to-copy-text-to-clipboard-in-javascript/)
- [關於 Clipboard API 主流瀏覽器支援程度分析](https://www.shirohana.me/blog/articles/2020-w3c-clipboard-api/)
- [Async Clipboard API：異步剪貼板 API](https://juejin.cn/post/6844903577928892430)
- [剪贴板操作 Clipboard API 教程](http://www.ruanyifeng.com/blog/2021/01/clipboard-api.html)
- [前端操作剪切板不完全指北](https://juejin.cn/post/6910550918761365511#heading-0)
- [知乎上複製回答，剪貼板裡自動加上版權出處的技術js如何實現？](https://www.zhihu.com/question/38685128/answer/77604309)
- [clipboard.js 的源碼分析](https://blog.csdn.net/qiwoo_weekly/article/details/111243750)
- [clipboard.js 核心代碼解析](https://juejin.cn/post/6844903985220943879)