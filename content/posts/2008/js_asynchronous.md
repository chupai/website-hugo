---
title: "非同步"
date: 2020-08-20T04:00:00+08:00
description: "非同步是 JavaScript 最重要的觀念之一。"
keywords: ["JavaScript", "非同步", "竹白"]
tags: ["JavaScript", "非同步"]
categories: ["JavaScript筆記"]
image: images/covers/200820_js_asynchronous.png
draft: false
---

>[JavaScript筆記 目錄](/posts/190620_javascript)

非同步是 JavaScript 最重要的觀念之一。
- [非同步](/posts/2008/js_asynchronous)
- [回呼函式](/posts/2008/js_callback)
- [Promise](/posts/2008/js_promise)
- [Async/Await](/posts/2008/js_async_await)

<!--more-->

{{< featuredImage >}}

## 什麼是非同步

- Synchronous 同步
- Asynchronous 非同步（異步）

同步就是程式碼由上到下依序執行；而非同步則是，不用等待該程式碼執行完畢，就可以往下繼續執行。

常見的非同步程式：
- 瀏覽器所提供的計時器 `setTimeout()`、`setInterval()`
- DOM 的事件監聽 `addEventListener()`
- 網路請求資料 Ajax 呼叫


## JavaScript 的非同步處理

首先，JavaScript 是 **單線程（single threaded）** 的程式語言，也就是一個單執行緒、同步的程式，它逐行執行程式碼，並不會非同步的執行程式。

那它是如何做到非同步執行呢？

簡單來說，JS 引擎在運行時，除了一個正在處理事情的主線程外，還有一個用來存放非同步需求的 **事件佇列（Event Queue）**。當偵測到非同步的需求，就會將它丟進去，等到主線程所有事情處理完畢，就會去 **事件佇列** 找事做。

JS 引擎是透過不斷的檢查**事件佇列**，來確認非同步的程式符合進入主線程的條件，這種檢查機制叫做 **事件迴圈（Event Loop）**。

換句話說，當某件事情卡住了，後續的事情都將無法繼續執行。

## 非同步的流程控制

如果有多個非同步操作，就存在一個流程控制的問題。

要如何解決非同步操作的執行順序，有以下幾種方式：
- 回呼函式 Callback（最早期）
- Promise 物件（ES6）
- Async/Await 語法（Promise 語法糖 ES8）

首先我們來看一個範例問題：
```javascript
function square(n) {
  return n * n;
}

const result = square(2);
console.log(result); // 4

```
這是個很普通的同步程式，`square()` 回傳平方結果，然後用 `console.log()` 印出。

接下來我們使用 `setTimeout()` 來模擬，需要經過冗長計算過程或從伺服器取得，而無法立即做運算。

會遇到的問題：
```javascript
function delaySquare(n) {
  let result;
  setTimeout(() => {
    result = n * n;
  }, 2000);
  return result;
}

const result = delaySquare(2);
console.log(result); // undefined
```
很明顯的，我們無法直接回傳非同步程式的運算結果。

那麼我們要如何解決？

### 1. 回呼函式

傳統作法就是新增一個 **回呼函式 `callback`** 參數，將結果當作引數傳入：
```javascript
function delaySquare(n, callback) {
  setTimeout(() => {
    callback(n * n);
  }, 2000);
}

delaySquare(2, (result) => {
  console.log(result);
});
```
但是，如果今天我要重複呼叫，就必須要等到上次的呼叫成功時，才能進行下次呼叫，就會產生 Callback Hell（回呼地獄）：
```javascript
delaySquare(2, (result) => {
  console.log(result);
  delaySquare(3, (result) => {
    console.log(result);
    delaySquare(4, (result) => {
      console.log(result);
      delaySquare(5, (result) => {
        console.log(result);
      });
    });
  });
});
```

由上述可知，沒有進一步的非同步需求，回呼函式是直覺而簡單的，但如果需要執行多次非同步程式，就會不利於程式碼的閱讀和維護。


從 ES6 開始，JavaScript 引入了一些新功能，可以幫助我們不用回呼就能處理非同步程式：
- Promise 物件（ES6）
- Async/Await 語法（Promise 語法糖 ES8）

### 2. Promise 物件

其實 Promise 已經存在多年，但是直到 ES6 才被標準化和引入。

```javascript
function delaySquare(n) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(n * n);
    }, 2000);
  });
}

delaySquare(2).then((result) => {
  console.log(result);
});
```

Promise 可以讓非同步操作寫起來，就像在寫同步操作的流程：
```javascript
delaySquare(2)
  .then((result) => {
    console.log(result);
    return delaySquare(3);
  })
  .then((result) => {
    console.log(result);
    return delaySquare(4);
  })
  .then((result) => {
    console.log(result);
    return delaySquare(5);
  });
```

### 3. Async/Await 語法

Async/Await 其實就是 Promise 的語法糖將其簡化。

```javascript
function delaySquare(n) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(n * n);
    }, 2000);
  });
}

async function answer() {
  const result = await delaySquare(2);
  console.log(result);
  const result2 = await delaySquare(3);
  console.log(result2);
  const result3 = await delaySquare(4);
  console.log(result3);
  const result4 = await delaySquare(5);
  console.log(result4);
}

answer();
```

必須使用 `async function` 宣告函式，才可以使用 `await`，而 `await` 呼叫的函式必須回傳 Promise 物件。

