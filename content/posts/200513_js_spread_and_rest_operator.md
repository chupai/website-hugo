---
title: "展開／其餘運算子"
date: 2020-05-13
description: "ES6 新增了... 運算子，但在不同位置，會有不同的功能，所以會有兩種叫法，分別為「展開」和「其餘」運算子。"
keywords: ["JavaScript", "展開運算子","其餘運算子","竹白"]
tags: ["JavaScript", "展開運算子","其餘運算子", "ES6"]
categories: ["JavaScript筆記"]
image: images/covers/200513_js_spread_and_rest_operator.png
draft: false
---

>[JavaScript筆記 目錄](/posts/190620_javascript)
<!--more-->
{{< featuredImage >}}

## ...  運算子

ES6 新增了 `...` 運算子，但在不同位置，會有不同的功能，所以會有兩種叫法：
- 展開運算子（Spread Operator）
- 其餘運算子（Rest Operator）

## 展開運算子
展開運算子（Spread Operator）可用於陣列、類陣列、物件，但使用地點有些微差異。

- 陣列、類陣列
  - 陣列字面值
  - 函式呼叫時作為引數
- 物件
  - 只能於物件字面值中使用

### 1. 基本用法

展開運算子會將陣列展開為個別的值。
```javascript
let number = [1, 2, 3];
let string = ['a', 'b', 'c'];

console.log(...number); // 1 2 3
console.log(...string); // 'a' 'b' 'c'

// 等同於
console.log(1, 2, 3);
console.log('a', 'b', 'c');
```

主要是用在呼叫函式時的要傳入引數：
```javascript
function foo(a, b, c) {
  console.log(a + b + c);
}

foo(...[1, 2, 3]); // 6

// 等同 apply()
foo.apply(null, [1, 2, 3]);  // 6
```
ES6 之前，如果要將陣列拆開當作引數傳入都會用函式的 [`apply()`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) 方法，但現在展開運算子完全可以取代它。

展開運算子除了用在陣列本身，也可以用在陣列字面值中，將陣列展開：
```javascript
let number = [2, 3];

console.log([1, ...number, 5]); // [1, 2, 3, 5]
```

用於合併陣列非常方便，原本需要使用 [`concat()`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/concat)，現在可以使用 `...` 來代替：
```javascript
let arr1 = [1, 2, 3];
let arr2 = [4, 5, 6];

let temp1 = arr1.concat(arr2);
console.log(temp1); // [1, 2, 3, 4, 5, 6]

let temp2 = [...arr1, ...arr2];
console.log(temp2); // [1, 2, 3, 4, 5, 6]
```

也可利用它來淺拷貝陣列：
```javascript
let arr1 = [1, 2, 3];
let arr2 = [...arr];

console.log(arr2); // [1, 2, 3]
console.log(arr1 === arr2); // false
```

### 2. 類陣列（Array-likes）

展開運算子不只是陣列可用，類陣列（Array-likes）也能使用，因為它具有索引和 `length` 屬性。

`arguments` 物件：
```javascript
function foo() {
  console.log(arguments instanceof Array); // false
  let arg = [...arguments];
  console.log(arg instanceof Array); // true
}

foo();
```

DOM 物件操作後所得到的串列：
```javascript
const list = document.getElementsByTagName('body');

console.log(list instanceof Array); // false
console.log([...list] instanceof Array);  // true
```

字串：
```javascript
let string = 'hello';

console.log([...'hello']);  // ['h', 'e', 'l', 'l', 'o']
```

### 2. 物件

展開運算子如果直接用在物件上會拋出錯誤，因為展開運算子只能用於具有索引和 `length` 屬性的物件。
```javascript
let obj = { a: 1, b: 2 };

console.log(...obj);  // Uncaught TypeError: Found non-callable @@iterator
```
你可以想成 `a: 1, b: 2` 沒東西裝。

但如果要展開一個物件，可於物件字面值內。你可以想成 `a: 1, b: 2` 外面還有一個 `{}` 裝它。

因此可以應用在淺拷貝物件上：
```javascript
let obj = { a: 1, b: 2 };
let obj2 = { ...obj };

console.log(obj2); // { a: 1, b: 2 }
console.log(obj === obj2); // false
```

## 其餘運算子

在函式宣告中的最後一個參數加上 `...` 運算子，會稱其為「其餘運算子（Rest Operator）」，它與展開運算子效果上剛好完全相反，其餘運算子是把許多的參數轉換成一個陣列。

```javascript
function foo(...args) {
  console.log(args);
}

foo(1, 2, 3); // [1, 2, 3]
```
其餘運算子因為是用於參數上，因此又稱為「其餘參數」，應用於參數不固定的函式上。

ES6 之前，會利用 `arguments` 物件來存取傳入的引數。
```javascript
function foo(var_args) {
  for (var i = 0; i < arguments.length; i++) {
    var tmp = arguments[i];
    if (typeof tmp !== 'number') {
      console.log('輸入的值不是數字：' + tmp);
    } else {
      console.log('輸入的值是數字：' + tmp);
    }
  }
}

foo(1, 2, 5, 'a');
// "test.js:6 輸入的值是數字：1"
// "test.js:6 輸入的值是數字：2"
// "test.js:6 輸入的值是數字：5"
// "test.js:4 輸入的值不是數字：a"
```
`arguments` 物件是類陣列，因此如果使用陣列方法需要將其轉換成陣列。

為了程式碼的可讀性，通常會給可變長度的參數一個假名稱，讓閱讀程式碼時，知道這裡是輸入可變長度的參數。

上段程式碼改用其餘運參數：
```javascript
function foo(...args) {
  args.forEach(i => {
    if (typeof i !== 'number') {
      console.log('輸入的值不是數字：' + i);
    } else {
      console.log('輸入的值是數字：' + i);
    }
  });
}

foo(1, 2, 5, 'a');
// "test.js:6 輸入的值是數字：1"
// "test.js:6 輸入的值是數字：2"
// "test.js:6 輸入的值是數字：5"
// "test.js:4 輸入的值不是數字：a"
```

如果使用箭頭函式，其餘運算子必須以小括號標記，即使函式只有一個參數：
```javascript
let sumAll = (...number) => number.reduce((total, next) => total + next);

console.log(sumAll(1, 3, 5)); // 9
```