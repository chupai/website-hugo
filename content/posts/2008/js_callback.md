---
title: "回呼函式"
date: 2020-08-20T04:01:00+08:00
description: "回呼函式（callback function）是指能藉由參數（argument）通往另一個函式的函式。"
keywords: ["JavaScript", "非同步","回呼函式", "竹白"]
tags: ["JavaScript", "非同步", "回呼函式"]
categories: ["JavaScript筆記"]
image: images/covers/200820_js_callback.png
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

## 什麼是回呼函式

回呼函式（callback function）是指能藉由參數（argument）通往另一個函式的函式。

簡單來說，就是這個函式做為另一個函式的引數傳入，就稱為「回呼函式」，例如常見的陣列處理方法都有回呼函式。

```javascript
function foo(name) {
  console.log(name);
}

function boo(name, callback) {
  callback(name);
}

boo('Peter', foo); // "Peter"

// 陣列批次處理方法
const arr = [1, 2, 3];
const newArr = arr.map((item) => item * 2);

console.log(newArr); // [2, 4, 6]
```

這些屬於同步回呼，因為它是立即執行的。

## 非同步

而回呼常用來延續非同步行動完成後的程式執行，例如計時器等等。
```javascript
// 計時器
setTimeout(() => {
  console.log('1');
}, 3000);
```

### 1. 回呼中回呼

舉例來說，如果要設定計時器，在它執行完後，在一次呼叫一個計時器。

錯誤寫法：
```javascript
setTimeout(() => {
  console.log('1');
}, 2000);

setTimeout(() => {
  console.log('2');
}, 2000);

// 兩秒後 同時印出
// 1
// 2
```

必須將第二個計時器寫在第一個回呼函式中：
```javascript
setTimeout(() => {
  console.log('1');
  setTimeout(() => {
    console.log('2');
  }, 2000);
}, 2000);
```

### 2. 回呼地域

非同步程式的回呼對於一個或兩個的簡單巢狀，這樣的呼叫看起來還好。

但對於一個接一個非同步動作來說，程式碼會變得非常難以維護。舉例來說，這是一個延遲呼叫的函式：

```javascript
function delay(n, callback) {
  setTimeout(() => {
    callback(n);
  }, 2000);
}

delay(1, (n) => {
  console.log(n);
});
```

上面有說過，如果要重複呼叫，就必須要等到上次的呼叫成功時，才能進行下次呼叫：
```javascript
delay(1, (n) => {
  console.log(n);
  delay(2, (n) => {
    console.log(n);
  });
});
```

如果呼叫次非常多，程式碼就會變成這樣：
```javascript
delay(1, (n) => {
  console.log(n);
  delay(2, (n) => {
    console.log(n);
    delay(3, (n) => {
      console.log(n);
      delay(4, (n) => {
        console.log(n);
        delay(5, (n) => {
          console.log(n);
          delay(6, (n) => {
            console.log(n);
          });
        });
      });
    });
  });
});
```

由上述可知，沒有進一步的非同步需求，回呼函式是直覺而簡單的，但如果需要執行多次非同步程式，就會不利於程式碼的閱讀和維護。

從 ES6 開始，JavaScript 引入了一些新功能，可以幫助我們不用回呼就能處理非同步程式：
- Promise 物件（ES6）
- Async/Await 語法（Promise 語法糖 ES8）