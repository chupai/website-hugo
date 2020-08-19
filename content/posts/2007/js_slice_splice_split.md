---
title: "Slice、Splice、Split 的區別"
date: 2020-07-28
description: "處理陣列或字串常見的方法。"
keywords: ["JavaScript", "陣列 Array", "字串 String", "竹白"]
tags: ["JavaScript", "陣列 Array", "字串 String"]
categories: ["JavaScript筆記"]
image: images/covers/200728_js_slice_splice_split.png
draft: false
---

>[JavaScript筆記 目錄](/posts/190620_javascript)

<!--more-->

{{< featuredImage >}}

## 前言

Slice、Splice、Split 這幾個方法都是 S 開頭，因此若不熟悉很容易搞混，但它們又是處理 **陣列** 或 **字串** 常見的方法。

- <a href="https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/slice" target="_blank">`Array.prototype.slice()`</a>
- <a href="https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/splice" target="_blank">`Array.prototype.splice()
`</a>
- <a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/split" target="_blank">`String.prototype.split()
`</a>
- <a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/slice" target="_blank">`String.prototype.slice()`</a>


## slice 提取

陣列和字串都有 `slice()` 方法。

### 1. Array.prototype.slice()

陣列的 `slice()` 會回傳一個指定索引範圍的新陣列（不改變原陣列）。

```javascript
arr.slice([begin[, end]])
```
- `begin`：起始索引，若省略預設為 `0`；
- `end`：停止索引（不包含自己），若省略預設為 `arr.length`；
- 可以使用負數索引，從末項開始倒數。

#### 1.1 範例

```javascript
const arr = [0, 1, 2, 3, 4, 5];

// 無參數 0 ~ (arr.length - 1)
console.log( arr.slice() ); // [0, 1, 2, 3, 4, 5]

// begin 2 ~ (arr.length - 1)
console.log( arr.slice(2) ); // [2, 3, 4, 5]

// begin end 2 ~ (3 - 1)
console.log( arr.slice(2, 4) ); // [2, 3]

// 負數 (arr.length - 2) ~ (arr.length - 1)
console.log( arr.slice(-2) );  // [4, 5]
```

#### 1.2 常見用法

用法一：淺拷貝陣列
```javascript
const arr = [0, 1, 2, 3, 4, 5];
const temp = arr.slice();
```
ES6 前沒展開運算子，都是使用 `slice()` 來淺拷貝陣列。

用法二：從陣列前端、末端取 N 個元素使用
```javascript
const arr = [0, 1, 2, 3, 4, 5];

const first4 = arr.slice(0, 4);
console.log( first4 );  // [0, 1, 2, 3]

const last4 = arr.slice(-4);
console.log( last4 );  // [2, 3, 4, 5]
```

用法三：獲取陣列中的某段長度的子陣列
```javascript
const arr = [0, 1, 2, 3, 4, 5];

function pullSegment(arr, begin, len) {
  return arr.slice(begin, begin + len);
}

console.log( pullSegment(arr, 2, 3) ); // [2, 3, 4]
```

用法四：縮短陣列長度
```javascript
let arr = [0, 1, 2, 3, 4, 5];

arr = arr.slice(0, 3);
console.log( arr ); // [0, 1, 2]
```

### 2. String.prototype.slice()

字串的 `slice()` 會回傳一個指定索引範圍字元的新字串（不改變原字串）。

```javascript
str.slice(beginIndex[, endIndex])
```
用法基本上與陣列的 `slice()` 差不多，直接看範例。

#### 2.1 範例

```javascript
const str = 'Hello world!';

console.log( str.slice() );     // 'Hello world!'
console.log( str.slice(2) );    // 'llo world!'
console.log( str.slice(2, 4) ); // 'll'
console.log( str.slice(-2) );   // 'd!'
```

#### 2.1 類似方法

類似的方法還有 <a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/substring" target="_blank">`substring()`</a>、<a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/substr" target="_blank">`substr()`</a> 但 `substr()` 它不屬於 JavaScript 核心規範，所以如果是非瀏覽器的環境可能無法使用，所以盡可能避免使用。而 `substring()` 因為第二個參數小於第一個參數時，會自動換位，而且不支援負數，沒有 `slice()` 來的彈性。


## Splice 拼接

`Array.prototype.splice()` 藉由刪除既有元素或加入新元素來改變一個陣列的內容，並回傳一個包含被刪除的元素陣列。

```javascript
array.splice(start[, deleteCount[, item1[, item2[, ...]]]])
```
- `start`：要插入或刪除的索引位置；
  - 可以使用負數索引，從末項開始倒數
  - 若大於 `arr.length` 則會被設為 0
- `deleteCount`：要刪除的元素數量；
  - 若省略或大於 `arr.length`，`start` 後的元素都會被刪除（包含 `start`）
- `item1, item2, ...`：要插入的元素內容。


### 1. 範例

從指定索引位置開始，刪除所有元素（含指定索引）：
```javascript
const arr = [0, 1, 2, 3, 4, 5];
const removed = arr.splice(2);

console.log(arr);     // [0, 1]
console.log(removed); // [2, 3, 4, 5]
```

從指定索引位置開始，刪除指定數量的元素：
```javascript
const arr = [0, 1, 2, 3, 4, 5];
const removed = arr.splice(2, 2);

console.log(arr);     // [0, 1, 4, 5]
console.log(removed); // [2, 3]
```

從指定索引位置開始，刪除指定數量的元素，並插入元素：
```javascript
const arr = [0, 1, 2, 3, 4, 5];
const removed = arr.splice(2, 3, 'aa');

console.log(arr); // [ 0, 1, 'aa', 5 ]
console.log(removed); // [2, 3, 4]
```

### 2. 常見用法

移除陣列指定元素：
```javascript
function removeElement(arr, index) {
  arr.splice(index, 1);
  return arr;
}

const arr = [0, 1, 2, 3, 4, 5];
removeElement(arr, 2);
console.log(arr); //  [0, 1, 3, 4, 5]
```

## split 分割

`String.prototype.split()` 方法會依照指定規則分割字串，並回傳一個陣列，內容為拆分的字串。

```javascript
str.split([separator[, limit]])
```
- `separator`：字串或正則表達式，從符合的字元位置分割，若為空字串，則分割每個字元；
- `limit`：回傳的分割元素數量。

### 1. 範例

```javascript
const monthString = 'Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec';

console.log( monthString.split(',') );
// [ 'Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec' ]


const num = '00000000';

console.log( num.split('', 2) ); // [ '0', '0' ]
```