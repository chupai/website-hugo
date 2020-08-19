---
title: "Async/Await"
date: 2020-08-20T04:03:00+08:00
description: "Async/Await 為 Promise 的語法糖。"
keywords: ["JavaScript", "非同步", "Promise", "Async/Await" ,"竹白"]
tags: ["JavaScript", "非同步", "Promise", "Async/Await"]
categories: ["JavaScript筆記"]
image: images/covers/200820_js_js_async_await.png
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

## 什麼是 Async/Await

ES8 新增 的 Async/Await 為 Promise 的語法糖，底層運作還是 Promise。

### 1. Async functions

在一個函式前面加上`async` 關鍵字放：
```javascript
async function fn() {}
```

這個函式只會回傳一個 Promise 物件，如果 `return` 值非 Promise ，會將 `return` 值作為 `resolve()` 的參數值。

```javascript
async function fn() {
  return 1;
}

fn().then(console.log);

// 1
```

也就是說 `async` 確保了函式的回傳值是一個 Promise 物件，也會包裝非 Promise 的值。

### 2. Await

`await` 只能用在 Async functions 中。

`await` 可以讓非同步程式在 Async functions 中用起來像是使寫同步程式一樣，依序執行。

這是一個隨機延遲時間的函式：
```javascript
function delay(value) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(value);
    }, Math.random() * 2000);
  });
}
```

如果依序呼叫三個函式，會根據延遲時間回傳，所以順序會不一定：
```javascript
function delay(value) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(value);
    }, Math.random() * 2000);
  });
}

function test() {
  delay(1).then(console.log);
  delay(2).then(console.log);
  delay(3).then(console.log);
}

test();
```

使用 Async/Await 就不需要使用 `then` 方法了，能直接獲得結果值：
```javascript
async function test() {
  console.log(await delay(1));
  console.log(await delay(2));
  console.log(await delay(3));
}

test();
// 1
// 2
// 3
```
每個函式都會依序執行，會等到處理完畢，再執行下一行，就像寫同步的程式一樣。

## 錯誤處理

如果要錯誤處理，要使用 `try...catch` 述句：

```javascript
async function fun() {
  const p = new Promise((resolve, reject) => {
    setTimeout(() => {
      const ran = Math.floor(Math.random() * 2); // 模擬可能失敗 0 or 1
      if (ran) {
        resolve('done!');
      } else {
        reject(new Error('error!'));
      }
    }, 1000);
  });

  try {
    const r = await p;
    console.log(r);
  } catch (err) {
    console.log(err);
  }
}

fun();
```