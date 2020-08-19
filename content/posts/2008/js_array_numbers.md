---
title: "連續的數字陣列"
date: 2020-08-18
description: "產生 0 ~ 99 的數字陣列。"
keywords: ["JavaScript", "陣列", "竹白"]
tags: ["JavaScript", "ES6", "陣列"]
categories: ["JavaScript筆記"]
image: images/covers/200818_js_array_numbers.png
draft: false
---

>[JavaScript筆記 目錄](/posts/190620_javascript)

產生 0 ~ 99 的數字陣列。

<!--more-->

{{< featuredImage >}}

## 迴圈

最簡單的方法就是使用迴圈。

`for` 迴圈：
```javascript
const numbers = [];

for (let i = 0; i < 100; i += 1) {
  numbers[i] = i;
}
```

或者使用 `push()`：
```javascript
const numbers = [];

for (let i = 0; i < 100; i += 1) {
  numbers.push(i);
}
```

`while` 迴圈：
```javascript
const numbers = [];
let i = 0;

while (i < 100) {
  numbers.push(i);
  i += 1;
}
```

## 一行程式碼

使用 [`Array.from`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/from) 內建的 `map()` 函式： 
```javascript
const numbers = Array.from({ length: 100 }, (v, k) => k);

// or
const numbers = Array.from(Array(100), (v, k) => k);
```


先使用 [`Object.keys`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Object/keys) 取得鍵名（陣列鍵名就是索引），再轉成陣列：
```javascript
const numbers = Array.from(Array(100).keys());

// or
const numbers = [...Array(100).keys()];
```