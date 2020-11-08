---
title: "Fetch"
date: 2020-08-23
description: Fetch 是近年來被提及將要取代 XMLHttpRequest 的技術新標準，是一個 HTML5 的 API。"
keywords: ["JavaScript", "非同步", "Fetch", "AJAX" ,"竹白"]
tags: ["JavaScript", "非同步", "Fetch", "AJAX"]
categories: ["JavaScript筆記"]
image: images/covers/2008/js_fetch.png
draft: false
---

>[JavaScript筆記 目錄](/posts/190620_javascript)

AJAX：
- [AJAX](/posts/2008/js_ajax)
- [JSON](/posts/2008/js_json)
- [XMLHttpRequest](/posts/2008/js_xhr)
- [Fetch](/posts/2008/js_fetch)

## Fetch API

傳統 AJAX 操作是使用 XMLHttpRequest（XHR）物件來實現，而 Fetch API 是近年來被提及將要取代 XMLHttpRequest 的技術新標準，是一個 HTML5 的 API。

### 1. 簡介

Fetch 並不是 XMLHttpRequest 的升級版本，不是 `jQuery` 提供的 `$.ajax` 語法或 AXIOS 那種原生 XHR 的封裝。它是一個全新的東西，並且基於 Promise 語法結構所設計，可配合使用 Async/Await 語法，使程式更加優雅。

Fetch 的解決了一些 XHR 問題，例如，解決回呼地獄，使用起來更加簡潔、不需要加載函式庫等等。

常被提及可能取代 XHR，但它也不是完全沒有缺點，沒辦法像 XHR 處理更細節的東西，例如，無法對請求進度、愈時請求做處理，只對網路請求報錯等等。

### 2. 範例

取得假資料 JSON，API：
```javascript
const url = 'https://randomuser.me/api/';
```

傳統 XMLHttpRequest 需要好幾行的操作：
```javascript
const xhr = new XMLHttpRequest();
xhr.open('GET', url);
xhr.send(null);

xhr.addEventListener('load', function (event) {
  if (this.status === 200) {
    const data = JSON.parse(this.responseText);
    console.log(data);
  }
});
```

而 Fetch 只需要短短幾行：
```javascript
fetch(url)
  .then((response) => response.json())
  .then((data) => {console.log(data);});
```

使用 Async/Await 更直覺：
```javascript
async function ajax() {
  const response = await fetch(url);
  const data = await response.json();
  console.log(data);
}

ajax();
```

## Request 物件

使用 Fetch 最簡單的方式就是透過 `fetch()`，但再這之前，我們先來理解什麼是 Request 物件，

Request 物件代表資源請求，建構式接受一個或兩個參數：
```javascript
const myRequest = new Request(input, [init]);
```
- `input`：url，獲取資源的網址；
- `init`：一個可選物件，包含希望被包括到請求中的各種自定義選項。

`init` 物件中選項如下：
- `method`：指定 <a href="https://developer.mozilla.org/zh-TW/docs/Web/HTTP/Methods" target="_blank">HTTP 請求方法</a>，例如 GET、POST、DELETE等等（預設為 GET）。
- `headers`：任何你想加到請求中的 Headers 物件（預設 `{}` 空物件）。
- `body`：需要發送的內容，如果 `method` 為 GET 或 HEAD 則不設定。
- `mode`：請求的模式。
  - `cors` 允許跨域（預設）
  - `no-cors` 只允許使用 HEAD、GET、POST 方法
  - `same-origin` 只允許同源請求，否則直接報錯
  - `navigate` 支持頁面導航
- `credentials`：表示是否發送 `cookie`。
  - `omit` 不發送 cookie
  - `same-origin` 僅在同源時發送 cookie（預設）
  - `include` 發送cookie
- `cache`：請求中想要使用處理緩存的策略，預設為`default`，其他可以參考 [cache mode ](https://developer.mozilla.org/en-US/docs/Web/API/Request/cache)。
- `redirect`：對重定向處理的模式
  - `manual` 需要用戶手動跟隨（預設）
  - `follow` 跟隨
  - `error` 發生錯誤
- `integrity`：包含一個用於驗證資資源完整性的字串。

## fetch 方法

fetch 方法有兩種呼叫方式：
1. 接受 Request 物件作為參數；
2. 符合建構 Request 物件的參數作為參數。

```javascript
const url = '';

const req = new Request(url, { method: 'GET'});
fetch(req);

// or
fetch(url, { method: 'GET'});
```

`fetch()` 函式會回傳一個狀態為 fulfilled 的 Promise 物件，`resolves` 為一個 Response 物件。

要注意的是，某些錯誤的 HTTP 狀態下如 404、500 等還是會回傳 fulfilled 狀態的 Promise。只有在有網絡錯誤會導致請求不能完成時，才會回傳 `rejected` 狀態的 Promise，因此需要做封裝處理才能判斷。


### 1. POST 範例

GET 因為是預設的請求的模式，所以不一定要設定參數，那麼我來看一下 POST 請求的範例：
```javascript
const url = '';
const sendData = {};

fetch(url, {
  method: 'POST',
  headers: { 'content-type': 'application/json' },
  body: JSON.stringify(sendData),
})
  .then((response) => response.json())
  .then((data) => {
    console.log(data);
  });
```

## Response 物件

Response 物件代表請求的回應。

### 1. 屬性

`fetch()` 只有在有網絡錯誤會導致請求不能完成時，才會回傳 rejected 狀態的 Promise，因此如果要查看 HTTP 錯誤，可以在回傳的 Response 物件中使用以下屬性：
- `status`：狀態代碼（整數）。
- `ok`：狀態結果（布林值），成功回傳 `true`（HTTP 狀態碼的範圍在 200-299），失敗回傳 `false`。

```javascript
fetch(url).then((response) => {
  console.log(response.ok);     // true
  console.log(response.status); // 200
});
```

Response 的其他屬性可以參考 <a href="https://developer.mozilla.org/zh-CN/docs/Web/API/Response#%E5%B1%9E%E6%80%A7" target="_blank">MDN</a>。

目前階段還沒辦法取得回應資料，接下會說明。

### 2. 方法

Response 物件中的 `body` 屬性提供了一個 ReadableStream 的實體，這個就是存放回應資料的地方。

我們可以使用以下 Response 提供的方法來讀取 `body` 屬性：
- `arrayBuffer()`
- `blob()`
- `json()`
- `text()`
- `formData()`

以上方法回傳一個被解析為對應格式的 Promise 物件。

`body` 屬性只能讀取一次，讀取過後 `bodyUsed` 屬性會變成 `true` 表示已讀，若再次呼叫方法讀取 `body` 屬性就會報錯（`TypeError: body stream is locked`），若要重複讀取可以使用 `clone()` 建立一份 Response 的複製物件。

```javascript
fetch(url)
  .then((response) => response.json())
  .then((data) => {console.log(data);});
```