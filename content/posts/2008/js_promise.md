---
title: "Promise"
date: 2020-08-20T04:02:00+08:00
description: "Promise 代表著「承諾」在未來提供任務執行結果。"
keywords: ["JavaScript", "非同步", "Promise","竹白"]
tags: ["JavaScript", "非同步", "Promise"]
categories: ["JavaScript筆記"]
image: images/covers/200820_js_promise.png
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

## 什麼是 Promise？

Promise 在其他不同語言有很多不同的名稱，例如 Delay、Future。其實 Promise API 已經存在多年，但是直到 ES6 才被標準化和引入。

Promise 字面意思為「承諾」或「約定」，白話一點的解釋就是「我承諾幫你做某件事，但會不會做到不一定，但最後一定會跟你說有做到或沒做到」。

當你拿到一個 Promise 的時候，代表在未來中這個 Promise 可能會有三種狀態：
1. 未定（pending）
2. 達成（fulfilled）
3. 拒絕（rejected）

狀態只會由 pending 變成 fulfilled 或 rejected。

接下來可以根據上面這兩種結果，決定下一步要做啥：
1. 履行承諾，繼續做預定好的下一件事
2. 毀約後，根據原因去做對應的處理

## 創建 Promise 物件

要建立一個 Promise 物件實體需要使用 `Promise()` 建構式：
```javascript
new Promise(function(resolve, reject) {
  // ...
});
```

建構式中接受一個函式作為參數，該函式稱為 `executor`（執行函式），當 Promise 實體被創建，就會立刻執行 `executor` 函式。

這函式又接收兩個參數，分別為 `resolve` 與 `reject`，這兩個參數也是函式，皆接收一個參數。

如果在 `executor` 函式中：
- 呼叫 `resolve(value)`
  - Promise 會從 pending 狀態進入 fulfilled 狀態
  - 如有值傳入，它將成為完成結果
- 呼叫 `reject(reason)`
  - Promise 會從 pending 狀態進入 rejected 狀態
  - 如有值傳入，它將被設為拒絕理由，值通常會是 `Error` 物件實體，表示拋出訊息
- 如果都沒呼叫，那 Promise 會一直保持 pending 狀態。

一但進入 fulfilled 或 rejected 狀態，Promise 就不會再接受任何狀態改變，所以重複呼叫 `resolve` 或 `reject` 是沒有意義的行為。

`resolve` 通常會用在非同步程式中，可以將非同步程式處理的結果傳出去：

```javascript
const p = new Promise((resolve) => {
  setTimeout(() => resolve('done!'), 1000);
});
```
經過一秒，呼叫 `resolve` 將字串 `done!` 當作完成結果，也就是 Promise 狀態經過一秒才由 pending 狀態進入 fulfilled 狀態。

假如非同步程式執行失敗時，就可以利用 `reject` 獲取錯誤訊息，讓我們模擬一下：
```javascript
const p = new Promise((resolve, reject) => {
  setTimeout(() => {
    const ran = Math.floor(Math.random() * 2); // 模擬可能失敗 0 或 1
    if (ran) {
      resolve('done!');
    } else {
      reject(new Error('error!'));
    }
  }, 1000);
});
```

另外，呼叫 `resolve` 或 `reject` 並不會終止 `executor` 函式的執行，後續的程式碼還使會執行：
```javascript
const p = new Promise((resolve) => {
  resolve('done!');
  console.log(123);
});
```

但一般來說，呼叫 `resolve` 或 `reject` 以後，Promise 的使命就完成了，後繼操作應該放到 `then` 方法中，而不應該直接寫在呼叫完 `resolve` 或 `reject` 的後方。

## 接下來

### 1. then 方法

接下來，我們來看如何從 Promise 中取出結果值。

`then` 方法是 Promise 中最重要的方法，標準有八成在定義它。

`then` 字面意思為「然後」或「接下來」，也就是 Promise 實體被創建後，可以使用 `then` 方法分別指定 fulfilled 和 rejected 狀態的回呼函式，來處理回傳值：

```javascript
p.then(fulfilledCallback[, rejectedCallback]);
```
- `fulfilledCallback`：當 Promise 為 `fulfilled` 狀態時被呼叫，接收的參數為 `resolve()` 回傳的結果。 
- `rejectedCallback`（可選）：當 Promise 為 rejected 狀態時被呼叫，接收的參數為 `reject()` 回傳的失敗訊息。

如果 `fulfilledCallback` 或 `fulfilled` 不是函式時，忽略跳過。

`then` 方法雖然是立即執行的，但它裡面的回呼函式會進入到**事件佇列**中，等待 Promise 改變狀態。如果 Promise 一直是 pending 狀態，也就是一直沒有回應，`then` 方法中的回呼函式就會一直等待。

一個簡單的範例，建立一個 `delay` 函式，回傳 Promise 物件：
```javascript
function delay(time) {
  return new Promise((resolve) => {
    const t = time * 1000;
    setTimeout(() => {
      resolve(`延遲了 ${time} 秒`);
    }, t);
  });
}

delay(2).then((result) => {
  console.log(result);
});
// (經過2秒
// "延遲了 2 秒"
```
經過了 2 秒，Promise 由 pending 狀態進入 fulfilled 狀態，就會觸發 `then` 方法綁定的回呼函式，印出結果。

如果 Promise 為 rejected 狀態，沒有處理會有 Uncaught 提示，因此如果有呼叫 `reject` 要加上 `rejected` 狀態的回呼函式：
```javascript
const p = new Promise((resolve, reject) => {
  setTimeout(() => {
    const ran = Math.floor(Math.random() * 2); // 模擬可能失敗
    if (ran) {
      resolve('done!');
    } else {
      reject('錯誤');
    }
  }, 1000);
});

p.then(
  (result) => {
    console.log(result);
  },
  (error) => {
    console.log(error);
  }
);
```

### 2. 呼叫順序

```javascript
const p = new Promise((resolve) => {
  console.log(1);
  resolve(4);
  console.log(2);
});

p.then((v) => {
  console.log(3);
  console.log(v);
  console.log(5);
});

console.log(6);
// 1
// 2
// 6
// 3
// 4
// 5
```

1. 當 Promise 實體被創建，就會立刻執行 `executor` 函式，所以先印出 `1`；
2. 就算 `resolve` 已呼叫，`executor` 函式並不會終止，所以印出 `2`；
3. `then` 方法中的回呼函式一開始就會進入到事件佇列中，當 Promise 狀態改變，並等到同步程式執行完畢，才會開始執行，因此會跳過，所以會先印出 `6`；
4. Promise 狀態改變了，同步程式執行也完了，因此執行 `then` 方法中的回呼函式，所以印出 `3` `4` `5`。

### 3. 串連

`then` 方法，會回傳一個新的 Promise 實體。因此可以用 `then()` 串連多個 Promise，形成一個 Promise 鏈。

`then` 方法回傳的新 Promise 會有以下幾種情況：
1. 回呼函式中，`return` 非 Promise 的值；
2. 回呼函式中，`return` Promise；
3. 回呼函式中，因為 `throw` 述句拋出錯誤；
4. 沒有對應狀態的回呼函式。

我們使用這個函式建立 Promise 來說明：
```javascript
function p(n, b) {
  return new Promise((resolve, reject) => {
    if (b) {
      resolve(n);
    } else {
      reject('erro');
    }
  });
}
```
參數 `n` 為結果值、`b` 用來控制狀態。

情況一：如果回呼函式中 `return` 一個非 Promise 的值，則會回傳一個 fulfilled 狀態的新 Promise，並且將 `return` 值作為 `resolve` 的參數值。

```javascript
// 情況一
p(1, true)
  .then((result) => {
    console.log(result);
    return 2;
  })
  .then((result) => {
    console.log(result);
  });
  
// 1
// 2
```

情況二：如果回呼函式 `return` 一個 Promise，會回傳與該 Promise 相同狀態、帶相同值的新 Promise。
```javascript
p(1, true)
  .then((result) => {
    console.log(result);
    return p(2, true);
  })
  .then((result) => {
    console.log(result);
  });

// 1
// 2
```

情況三：`then` 方法中回呼函式因為 `throw` 述句拋出一個錯誤，則會回傳一個 rejected 狀態的 Promise，並且將錯誤訊息作為 `reject` 的參數值。

```javascript
p(1, true)
  .then((result) => {
    throw new Error('Something failed');
  })
  .then(null, (error) => {
    console.log(error);
  });

// "Error: Something failed"
```

情況四：如果 `then` 方法沒有可以處 Promise 狀態的回呼函式，會回傳與原本相同狀態、帶相同值的新 Promise。

```javascript
p(1, false)
  .then((v) => {
    console.log(v);
  })
  .then(null, (error) => {
    console.log(error);
  });
  
// "erro"
```


### 4. catch 方法

`catch` 方法，其實就是 `then(null, rejectedCallback)` 或 `then(undefined, rejectedCallback)` 的簡寫。

原本是使用 `then` 方法的兩個回呼函式，分別處理兩個狀態：
```javascript
p.then(
  (result) => {
    console.log(result);
  },
  (error) => {
    console.log(error);
  }
);
```

但建議改用以下方式：
```javascript
p.then((result) => {
  console.log(result);
}).catch((error) => {
  console.log(error);
});
```

這樣不但將兩種狀態處理分開寫，增加可讀性。如果 `then()` 的回呼函式因為 `throw` 述句拋出錯誤，`catch()` 也能捕捉錯誤訊息。

### 5. throw 與 reject

在 Promise 建構函式的 `executor` 函式中，直接使用 `throw` 述句相當於呼叫 `reject` 的作用。

```javascript
const p = new Promise((resolve, reject) => {
  throw new Error('error!');
  // 等同
  // reject(new Error('error!'))
});

p.catch((e) => {
  console.log(e.message);
});
// "error!"
```

### 6. finally 方法

在 ES7 新增了 `finally` 方法，不論 Promise 狀態是 fulfilled 或 rejected 都會執行。

`finally` 方法中的回呼函式不接受任何參數，也就是不會得到任何的回傳值，通常用於最後，用來處理與狀態無關的操作，例如重置、清除、終止。

原本需要使用 `then` 方法，將兩個回呼函式設置相同來處理，使用 `finally` 方法就只需要寫一次。

```javascript
p.finally(() => {
  console.log('End!');
});

// 等同
p.then(
  () => {
    console.log('End!');
  },
  (reason) => {
    console.log('End!');
    throw reason;
  }
);
```


## Promise 的靜態方法

### 1. all 方法

`Promise.all` 方法用於將多個 Promise，包裝成一個新的 Promise。

假設有一個延遲計算的函式：
```javascript
function delayAdd(x, y) {
  return new Promise((resolve) => {
    setTimeout(() => {
      const r = x + y;
      resolve(r);
    }, Math.random() * 2000);
  });
}
```

要如何將這三個的結果值相加呢？

```javascript
const p1 = delayAdd(1, 2);
const p2 = delayAdd(3, 4);
const p3 = delayAdd(5, 6);
```

使用 `Promise.all` 方法：
```javascript
Promise.all([p1, p2, p3]).then((result) => {
  const sum = result.reduce((total, value) => total + value);
  console.log(sum);
});
// 21
```

**語法說明：**
```javascript
Promise.all(iterable);
```
`iterable`：通常會是一個陣列，陣列中的值只會是一般的值或是 Promise 物件。

`Promise.all` 方法會將陣列中的值並行運算執行，全部完成後後會回傳一個 finally 狀態的 Promise。

有幾點要注意：
1. 陣列中的索引值與執行順序無關；
2. 陣列中的值如果不是 Promise 物件會自動使用`Promise.resolve` 方法來轉換；
3. 陣列中只要其中一個 Promise 狀態為 `rejected`，就會回傳 `rejected` 狀態的 Promise。

`Promise.all` 方法最後獲得的 Promise 的結果值會是一個陣列，陣列中的內容為所有 Promise 的結果。

```javascript
const p1 = delayAdd(1, 2);
const p2 = delayAdd(3, 4);
const p3 = delayAdd(5, 6);

Promise.all([p1, p2, p3]).then((result) => {
  console.log(result);
});
// [3, 7, 11]
```

### 2. race 方法

`Promise.race` 方法的規則與 `Promise.all` 方法相同，差異在於 `Promise.race` 回傳的 Promise 的結果只有最快變成 `finally` 狀態的那個。

```javascript
function delay(name, time) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(name);
    }, time);
  });
}


const p1 = delay('A', 2000);
const p2 = delay('B', 100);
const p3 = delay('C', 500);

Promise.race([p1, p2, p3]).then((result) => {
  console.log(result);
});

// "B"
```

如果同時變為 `finally` 狀態，就會以陣列順序優先者作為回傳值。

### 3. resolve 方法

`Promise.resolve()` 能建立一個 `finally` 狀態的 Promise 物件，其參數為結果值。

```javascript
const p = Promise.resolve(1);
```
等同：
```javascript
const p = new Promise((resolve) => {
  resolve(1);
});
```

### 4. reject 方法

`Promise.reject()` 能建立一個 `rejected` 狀態的 Promise 物件，其參數為錯誤訊息。

```javascript
const p = Promise.reject(new Error('error!'));
```

等同：
```javascript
const p = new Promise((resolve, reject) => {
  reject(new Error('error!'));
});
```