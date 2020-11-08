---
title: "XMLHttpRequest"
date: 2020-08-22
description: AJAX 技術在瀏覽器上，會利用 XMLHttpRequest 物件與伺服器進行非同步傳遞。"
keywords: ["JavaScript", "非同步", "XMLHttpRequest", "AJAX" ,"竹白"]
tags: ["JavaScript", "非同步", "XMLHttpRequest", "AJAX"]
categories: ["JavaScript筆記"]
image: images/covers/2008/js_xhr.png
draft: false
---

>[JavaScript筆記 目錄](/posts/190620_javascript)

AJAX：
- [AJAX](/posts/2008/js_ajax)
- [JSON](/posts/2008/js_json)
- [XMLHttpRequest](/posts/2008/js_xhr)
- [Fetch](/posts/2008/js_fetch)

<!--more-->

{{< featuredImage >}}

## XMLHttpRequest 

雖然現在很少人會直接使用原生 <a href="https://developer.mozilla.org/zh-TW/docs/Web/API/XMLHttpRequest" target="_blank">XMLHttpRequest 物件</a> 來處理 AJAX，但還是需要理解運作原理。

### 1. 請求

基本三步驟：
1. 建立一個 XMLHttpRequest 物件；
2. 設定請求；
3. 傳送請求。

```javascript
// 建立 XMLHttpRequest 物件
const xhr = new XMLHttpRequest();

// 設定請求
xhr.open('GET', url, true);

// 傳送請求
xhr.send(null);
```

#### 1.1 open 方法

<a href="https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/open" target="_blank">`XMLHttpRequest.open()`</a> 可以設定一個請求。

```javascript
xhr.open(method, url, [async, user, password]);
```
必要參數：
- `method`：指定 <a href="https://developer.mozilla.org/zh-TW/docs/Web/HTTP/Methods" target="_blank">HTTP 請求方法</a>，例如 GET、POST、DELETE等等。
- `url`：為請求的伺服器網址。

可選參數：
- `async`：預設為 `true` 表示要用非同步的方式進行，如果設為 `false` 則會以同步的方式來發送請求。
- `user`、`password`：預設為 `null`，為使用者名稱、密碼，視伺服器有無要求驗證而設置。

#### 1.2 send 方法

<a href="https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/send" target="_blank">`XMLHttpRequest.send()`</a> 用來對伺服器傳送請求。

```javascript
xhr.send([body]);
```
可選參數 `body` 資料本體，若使用 `GET` 請求，不帶資料時，可省略不寫或是傳入 `null`。

### 2. 伺服器回應

當瀏覽器從伺服器端接收資料回應後，便會觸發 `load` 事件，因此可以藉由事件監聽，先判斷回應狀態：

```javascript
xhr.addEventListener('load', function () {
  // 分析 HTTP 狀態
  if (this.status === 200) {
    // 處理回應資料
  }
});
```

<a href="https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/status" target="_blank">`XMLHttpRequest.status`</a> 會回傳 XMLHttpRequest 物件目前的狀態碼，可用來確認回應是否成功。

HTTP 狀態碼：
- 1xx: 資訊回應；
- 2xx: 成功回應；
- 3xx: 重定向；
- 4xx: 用戶端錯誤；
- 5xx: 伺服器端錯誤；
- 詳細 <a href="https://developer.mozilla.org/zh-TW/docs/Web/HTTP/Status" target="_blank">HTTP 狀態碼</a> 請參考 MDN。

除了 `load` 事件外，還有很多事件：
- `loadstart`：請求開始；
- `progress`：請求進度；
- `abort`：請求終止；
- `error`：發生連接錯誤；
- `load`：請求成功完成；
- `timeout`：請求超時；
- `loadend`：請求結束。

除了監聽 `load` 事件外，也可以監聽 `onreadystatechange` 事件。在發一個請求後，如果想追蹤請求當前處於哪種狀態，可以使用 <a href="https://developer.mozilla.org/zh-TW/docs/Web/API/XMLHttpRequest/readyState" target="_blank">`XMLHttpRequest.readyState`</a>，查看請求狀態：
- `0`：UNSENT 狀態，客戶端已被建立，但 `open()` 方法尚未被呼叫。
- `1`：OPENED 狀態，`open()` 方法已被呼叫，但還沒傳送資料。
- `2`：HEADERS_RECEIVED 狀態，`send()` 方法已被呼叫，而且可取得 header 與狀態。
- `3`：LOADING 狀態，回應資料下載中，此時 `responseText` 會擁有部分資料。
- `4`：DONE 狀態，完成下載操作。

`readyState = 4` 就類似 `loadend` 事件：
```javascript
xhr.addEventListener('onreadystatechange', function () {
  // 分析 xhr 狀態
  if (this.readyState === 4 && this.status === 200) {
    // 處理伺服器端回傳資料的程式碼
  }
});
```

### 3. 處理回應資料

確認伺服器成功回應，接下來就是處理資料。

共有三種方式，可以獲取請求後的回應資料：
- <a href="https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/response" target="_blank">`XMLHttpRequest.response`</a>
- <a href="https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/responseText" target="_blank">`XMLHttpRequest.responseText`</a>
- <a href="https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/responseXML" target="_blank">`XMLHttpRequest.responseXML`</a>

`responseText` 回傳 `DOMString`，`responseXML` 回傳 HTML 或 XML 的 `Document`。而 `response` 回傳的資料類型取決於 <a href="https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/responseType" target="_blank">`XMLHttpRequest.responseType`</a>。


範例，GET 一個 JSON 資料並轉成物件：
```javascript
const xhr = new XMLHttpRequest();
const url = '';  // API網址
xhr.open('GET', url);
xhr.send(null);

xhr.addEventListener('load', function (event) {
  if (this.status === 200) {
    const data = JSON.parse(this.responseText);
  }
});
```

## HTTP Header

使用 POST 請求時，`send()` 傳送資料給伺服器前，還需要使用 <a href="https://developer.mozilla.org/zh-TW/docs/Web/API/XMLHttpRequest/setRequestHeader" target="_blank">`XMLHttpRequest.setRequestHeader()`</a> 設定內容類型，告知伺服器我們傳遞的資料格式。

舉例來說，如果我們要傳遞的是 JSON 格式的資料：
```javascript
const xhr = new XMLHttpRequest();
const url = ''; // API網址
xhr.open('POST', url);

let sendData = {};  // 要傳遞的資料
xhr.setRequestHeader('content-type', 'application/json');
xhr.send(JSON.stringify(sendData));
```

語法：
```javascript
xhr.setRequestHeader(header, value);
```
- `header`：表頭名稱。
- `value`：編碼類型。

模擬 HTTP 回應工具 <a href="https://www.mocky.io/" target="_blank">Mocky</a>

## FormData 物件

若使用 <a href="https://developer.mozilla.org/zh-TW/docs/Web/API/FormData" target="_blank">FormData</a> 物件從表單獲取資料，可以直接使用 `send()` 傳送，`setRequestHeader()` 會自動設置。