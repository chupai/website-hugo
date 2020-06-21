---
title: "迭代陣列"
date: 2020-04-25
description: "對陣列做迭代是最常見的陣列操作。"
keywords: ["JavaScript", "陣列 Array","竹白"]
tags: ["JavaScript", "陣列 Array"]
categories: ["JavaScript筆記"]
image: images/covers/200425_js_array_iterate.png
draft: false
---

>[JavaScript筆記 目錄](/posts/190620_javascript)
<!--more-->
{{< featuredImage >}}

## 對陣列做迭代

對陣列做迭代是最常見的陣列操作，最基本的的迭代方式就是使用 `for` 迴圈：
```javascript
let arr = ['a', 'b', 'c'];

for (let i = 0; i < arr.length; i++) {
  console.log(arr[i]);
}

// "a"
// "b"
// "c"
```

但如果需要對陣列從頭到尾的迭代，使用 `for` 迴圈太繁瑣，需要索引值、結束條件。

因此可以改用 `forEach` 方法或 ES6 的 `for of` 迴圈來迭代陣列。

| 類型 | 效能 | 說明 |
| --- | --- |--- |
| `for` | 最高 | 雖然繁瑣，但自由度高，可使用變數改變索引值，也可以使用 `break`、`return`、`continue` 等語句 |
| `for in` | 最低 | 可用於物件，但不建議用於陣列 |
| `forEach` | 中間 | 搭配箭頭函式可使程式碼更簡潔 |
| `for of` | 中間（高於 `forEach`） | 簡潔，而且比起 `forEach` 可使用 `break`、`return`、`continue` 等語句，但沒有索引 |

### 1. forEach

首先我們來看看 `forEach` 方法，它會將陣列內的每個元素，皆傳入並執行給定的函式一次，不會產生新的陣列。

語法如下：
```javascript
arr.forEach(function callback(currentValue[, index[, array]]) {
    //your iterator
}[, thisArg]);
```
- `callback` 函式共有三個參數：
  - `currentValue`：陣列當前元素
  - `index`：陣列當元素的索引值
  - `array`：陣列本身
- `thisArg`：執行 `callback` 回呼函式的 `this`（即參考之 Object）值
- 回傳值： `undefined`


`currentValue` 為必填參數，而 `index` 與 `array` 則選擇性。

試著改寫開頭的 `for` 迴圈，達到結果。
```javascript
let arr = ['a', 'b', 'c'];

arr.forEach(function(item) {
  console.log(item);
})

// "a"
// "b"
// "c"

// 使用箭頭函式
arr.forEach((item) => console.log(item));
```

當傳入參數 `thisArg`，`callback` 函式的 `this` 就會指向你所傳入的物件。
```javascript
const arr = [1, 2, 3];
const arr2 = ['一', '二', '三'];

arr.forEach(function(item, index) {
  console.log(item + '：' + this[index]);
}, arr2);

// "1：一"
// "2：二"
// "3：三"
```

### 2. for of 迴圈

在 ES6 新增了 `for of` 迴圈，它可以用於可迭代物件（Array、Map、Set、String、TypedArray，arguments）上，也就是只要是實現了 `Interable` 介面的資料型別都能被遍歷。

```javascript
let arr = ['a', 'b', 'c'];

for (let element of arr) {
  console.log(element);
}

// "a"
// "b"
// "c"
```

使用 `for of` 遍與 `forEach` 的最大差異就是可以中斷：
```javascript
let arr = ['a', 'b', 'c', 'd', 'e'];

for (let element of arr) {
  if (element === 'c') {
    break;
  }
  console.log(element);
}

// "a"
// "b"
```

## 操作陣列元素

處理陣列中的元素，並不更動原陣列是常見的操作。

使用 `forEach` 方法，須先建立一個新的陣列搭配 `push` 方法。

舉例，合併姓名：
```javascript
const inventors = [
  { first: 'Albert', last: 'Einstein' },
  { first: 'Isaac', last: 'Newton' },
];

const names = [];

inventors.forEach((item) => {
  names.push(`${item.first} ${item.last}`);
});

console.log(names);  // ["Albert Einstein", "Isaac Newton"]
```

如果改用 `map` 方法：
```javascript
inventors.map(function(item) {
  return `${item.first} ${item.last}`;
});

// 箭頭函式
inventors.map(item => `${item.first} ${item.last}`);
```

`map` 方法會建立一個新的陣列，其內容為原陣列的每一個元素經由回呼函式運算後所回傳的結果之集合。

參數基本上與 `forEach()` 相同，但回呼函式多了一個 `return` 可以將會將處理結果放到新的陣列中，並回傳新的陣列。

讓我們來實作一下 `map` 方法，瞭解其原理：
```javaScript
function map(arr, callback) {
  let result = [];
  for (let i = 0; i < arr.length; i++) {
    result.push(callback(arr[i]));
  }
  return result;
}

map(inventors, (item) => `${item.first} ${item.last}`);
```

其他陣列迭代操作方法：
- [`map()`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/map) ：執行結果存到新陣列。
- [`filter()`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)：將符合條件的元素存到新陣列。
- [`find()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/find)：找到第一個符合條件的元素。
- [`some()`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/some) ：判斷有元素符合條件。
- [`every()`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/every)：判斷所有元素都符合條件。
- [`reduce()`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)：根據規則縮減陣列。

### 1. 過濾陣列

`filter` 方法會建立一個經指定之函式運算後，由原陣列中通過該函式檢驗之元素所構成的新陣列。 `return` 是用來判斷條件。如果 `return` 值為 `true` 會將該元素放入新陣列。

通常用來篩選資料：
```javascript
const numbers = [33, 22, 66, 88, 10, 5, 6, 9];

const bigNum = numbers.filter(function(item) {
  return item > 30;
});

console.log( bigNum );  // [33, 66, 88]
```
找出大於 30 的數值。

### 3. 陣列搜尋

`some` 方法會測試陣列中是否至少有一個元素通過由給定之函式所實作的測試。`return` 判斷條件。如果至少 `return` 一次 `true` 則回傳 `true`。

尋找陣列中使否有元素是奇數值：
```javascript
const array = [1, 2, 3, 4, 5];

const arraySome = array.some(function (even) { 
  return even % 2 === 0;
});

console.log( arraySome ); 
// true
```

要在一個陣列中搜尋某個值，一般來說會使用 `indexOf()`，它會回傳第一個符合的值的索引，沒找到回傳 `-1`。
```javascript
const arr = [1, 2, 3, 4, 5];

console.log(arr.indexOf(2)); // 1
```
但 `indexOf()` 是使用 `===` 來進行比較，因此如果傳入字串`'2'`，就會找不到。
```javascript
// 承接上段程式碼

console.log(arr.indexOf('2')); // -1
```

在 ES5 時，會使用 `some()` 來變通比對邏輯，但也只能判斷該陣列是否擁有該元素，無法實際獲取實際符合條件元素。
```javascript
// 承接上段程式碼

let result = arr.some(function(even) {
  return even == '2';
})

console.log(result);  // true
```

而到了 ES6 新增了 `find()`，解決了這個問題。
`find` 方法會回傳第一個滿足所提供之測試函式的成員值。否則回傳 `undefined`。與 `some()` 的差異在於，回傳第一個 `return` 值為 `true`  的元素。

找出第一個符合條件的元素：
```javascript
const inventory = [
  {name: 'apples', quantity: 2},
  {name: 'bananas', quantity: 0},
  {name: 'cherries', quantity: 5}
];

const inventoryFind = inventory.find(function(item) { 
  return item.name === 'cherries';
});

console.log( inventoryFind ); 
// { name: 'cherries', quantity: 5 }
```

[`findIndex()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex) 也是 ES6 新增的方法，`findIndex()` 與 `find()` 差異只在於，其會回傳其索引，如果沒有符合的元素，將返回 `-1`。
```javascript
// 承接上段程式碼

const inventoryIndexFind = inventory.findIndex(function(item) {
  return item.name === 'cherries';
});

console.log(inventoryIndexFind);
// 2
```

因此 `find()` 可以應用在更新資料，而 `findIndex()` 會用來刪除資料。
```javascript
const cart = [
  {id: 1, count: 0},
  {id: 2, count: 0},
  {id: 3, count: 0},
  {id: 4, count: 0},
];
```
找出 `id` 為 `3` 並將 `count` 更新成 `5`：
```javascript
let item = cart.find((item)=> item.id === 3);

item.count = 5;

console.log( cart );
// [
//   {id: 1, count: 0},
//   {id: 2, count: 0},
//   {id: 3, count: 5},
//   {id: 4, count: 0},
// ]
```

刪除 `id` 為 `3` 的元素：
```javascript
const cart = [
  {id: 1, count: 0},
  {id: 2, count: 0},
  {id: 3, count: 0},
  {id: 4, count: 0},
];

let index = cart.findIndex((item)=> item.id === 3);

cart.splice(index, 1);

console.log( cart );
// [
//   {id: 1, count: 0},
//   {id: 2, count: 0},
//   {id: 4, count: 0},
// ]
```

[`every`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/every) 方法會測試陣列中的所有元素是否都通過了由給定之函式所實作的測試。

與 `some()` 的差異在於，`every()` 必須要全部的元素都符合條件才會回傳 `true`。

是否全部都是基數：
```javascript
const array = [1, 2, 3, 4, 5];

const arrayEvery = array.every(function (even) { 
  return even % 2 === 0;
});

console.log( arrayEvery ); 
// false
```

### 4. reduce

`reduce` 方法將一個累加器及陣列中每項元素（由左至右）傳入回呼函式，將陣列化為單一值。其實就是帶有暫存器的 `forEach()`。

語法：
```javascript
arr.reduce(callback[accumulator, currentValue, currentIndex, array], initialValue)
```
- `accumulator`：用來累積回呼函式回傳值的累加器，若有提供的話，詳如下敘。累加器是上一次呼叫後，所回傳的累加數值。
- `initialValue`：於第一次呼叫 callback 時要傳入的累加器初始值。若沒有提供初始值，則原陣列的第一個元素將會被當作初始的累加器。假如於一個空陣列呼叫 `reduce()` 方法且沒有提供累加器初始值，將會發生錯誤。
- `return`：放入累加器中

假設要計算一個數值陣列的總合值為多少，使用 `for` 迴圈都會額外宣告一個 ` total ` 變數來當作累加器。
```javascript
const numbers = [1, 2, 3, 4, 5, 6];
let total = 0;

for(let i = 0; i < numbers.length; i++) {
  total += numbers[i];
}

console.log( total );
// 21
```

使用 `reduce()`：
```javascript
const numbers = [1, 2, 3, 4, 5, 6];

const numTotal = numbers.reduce(function(total, item) {
  return total + item
}, 0);

console.log( numTotal );
// 21
```

而 `reduceRight()` 基本上與 `reduce()` 一樣，差異是從右到左進行累加。

我們用扁平化（flatten）一個元素為陣列的陣列來觀察差異：
```javascript
const arr = [[0, 1], [2, 3], [4, 5]];

function callback(a, b) {
  return a.concat(b);
}

let result1 = arr.reduce(callback, []);
let result2 = arr.reduceRight(callback, []);

console.log(result1); // [0, 1, 2, 3, 4, 5]
console.log(result2); // [4, 5, 2, 3, 0, 1]
```

