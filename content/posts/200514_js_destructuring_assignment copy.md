---
title: "解構指定"
date: 2020-05-14
description: "ES6 引進了一個新語法功能，解構指定（destructuring assignment）或稱解構賦值，可以想成是一種有結構的指定。"
keywords: ["JavaScript", "解構指定","竹白"]
tags: ["JavaScript", "解構指定", "ES6"]
categories: ["JavaScript筆記"]
image: images/covers/200514_js_destructuring_assignment.png
draft: false
---

>[JavaScript筆記 目錄](/posts/190620_javascript)
<!--more-->
{{< featuredImage >}}

## 解構指定

ES6 引進了一個新語法功能，解構指定（destructuring assignment）或稱解構賦值，可以想成是一種有結構的指定。

在 JavaScript 中最常用的兩種資料結構為陣列與物件，而解構指定用於提取陣列或物件中的資料，存放到一系列的變數中。

請考慮以下程式碼：
```javascript
function foo() {
  return [1, 2, 3];
}
```
如果有一個函式回傳了一個陣列，要將陣列中的內容一一用變數儲存。

傳統作法為：
```javascript
var tmp = foo();
var a = tmp[0];
var b = tmp[1];
var c = tmp[2];

console.log(a, b, c); // 1 2 3
```
必須先將函式結果儲存，在一個一個宣告變數給值。

但如果改用新語法：
```javascript
let [a, b ,c] = foo();

console.log(a, b, c); // 1 2 3
```
就這麼麼簡單。

### 1. 陣列解構

陣列解構的基本語法：
```javascript
let [a,b] = [1, 2];

console.log(a, b); // 1 2
```

### 2. 物件解構

物件解構的基本語法：
```javascript
let { a, b } = { x: 1, y: 2 };

console.log(a, b); // 1 2
```

左邊實際上是 **物件字面值的簡短語法：
```javascript
// 等同
let { a: a, b: b } = { a: 1, b: 2 };

console.log(a, b); // 1 2
```

左側的屬性值屬於變數名稱：
```javascript
let { a: x, b: y } = { a: 1, b: 2 };

console.log(x, y); // 1 2
```

必須注意，左側的必須有對應的的屬性名才能指定：
```javascript
let { a } = { b: 1 };

console.log(a); // undefined 
```

屬性順序並不重要，只要有對應的屬性名即可：
```javascript
let { a, b } = { b: 2, a: 1 };

console.log(a, b); // 1 2
```

允許同一個屬性被列出多次：
```javascript
let { a: x, a: y } = { a: 1 };

console.log(x, y);  // 1 1
```
這表示能夠解構一個子物件，並且捕捉那個子物件，舉例來說：
```javascript
let {
  a: { x: X, x: Y },
  a: a,
} = { a: { x: 1 } };

console.log(X); // 1
console.log(Y); // 1
console.log(a); // { x: 1 }
```

子陣列也可以：
```javascript
let { a: X, a: Y, a: [Z] } = { a: [1] };

console.log(X); // [1]
console.log(Y); // [1]
console.log(Z); // 1
```

### 3. 不只是宣告

指定就是使用 `=` 運算子，所以解構指定不一定只能用在宣告上。

舉例來說：
```javascript
let a,b;

[a, b] = [1, 2];
console.log(a, b); // 1 2
```
變數已宣告，解構只負責進行指定動作。

但如果是物件就需要注意：
```javascript
let a,b;

{a, b} = {a:1, b: 2}; // SyntaxError: Unexpected token '='
```
會拋出錯誤是因為 `{a, b}` 的 `{}` 被解析成區塊，而非物件字面值，因此可以加上 `()`，避免這種情況：
```javascript
let a, b;

({ a, b } = { a: 1, b: 2 });
console.log(a, b); // 1 2
```

## 應用

交換值：
```javascript
let a = 1;
let b = 2;

[a, b] = [b, a];

console.log(a, b); // 2 1
```

建立物件映射（map）：
```javascript
let o1 = { a: 1, b: 2, c: 3 };
let o2 = {};

({ a: o2.x, b: o2.y, c: o2.z } = o1);

console.log(o2); // {x: 1, y: 2, z: 3}
```

或是將一個物件映射至陣列，或是反過來：
```javascript

let o1 = { a: 1, b: 2, c: 3 };
let a2 = [];

({ a: a2[0], b: a2[1], c: a2[2] } = o1);
console.log(a2); // [1, 2, 3]

// 反過來
let a1 = [1, 2, 3];
let o2 = {};

[o2.a, o2.b, o2.c] = a1;
console.log(o2);  // {a: 1, b: 2, c: 3}
```

## 解構指定運算式

解構指定運算式會回傳右邊完整的物件或陣列。
```javascript
let o = {a:1, b:2};
let a,b;

console.log({a,b} = o); // {a: 1, b: 2}
```
驗證：
```javascript
let obj = { a: 1, b: 2 };
let a, b, obj2;

obj2 = { a, b } = obj;

console.log(obj2 === obj); // ture
```

陣列也是如此：
```javascript
let arr = [1, 2];
let a, b, arr2;

arr2 = [a, b] = arr;

console.log(arr === arr2); // ture
```

## 解構的數量

不必指定所有出現的值：
```javascript
let [a] = [1, 2, 3, 4];
let [, b] = [1, 2, 3, 4];

console.log(a); // 1
console.log(b); // 2

let {c} = {a:1, b:2, c: 3};

console.log(c);  // 3
```
也可以留空來略過某些值。

如果指定過多的值，變數沒有對應值，內容就會是 `undefined`。
```javascript
let [a, b, c] = [1, 2];

console.log(c); // undefined

let { x, y } = { x: 1 };

console.log(y); // undefined
```

### 1. 展開運算子

在解構中，使用展開運算子，將會收集那些多出來的值。

陣列：
```javascript
let [a, ...b] = [1, 2, 3, 4];

console.log(a); // 1
console.log(b); // [2, 3, 4]
```
物件：
```javascript
let { a, ...b } = { a: 1, b: 2, c: 3, d: 4 };

console.log(a); // 1
console.log(b); // {b: 2, c: 3, d: 4}
```

## 預設值

使用類似預設函式引數的 `=` 語法，就可以給予變數預設值。
```javascript
let [a = 1, b = 2] = [10];

console.log(a); // 10
console.log(b); // 2

let { x = 5, y = 10 } = { x: 20 };

console.log(x); // 20
console.log(y); // 10
```

## 參數解構

一個簡單的函式與呼叫：
```javascript
function foo(x) {
  console.log(x);
}

foo(42);
```
`foo(42)` 執行的時候，引數 `42` 被指定給了參數 `x`。由上述可合理推論解解構指定，當然也可以用於函式參數。

參數的解構：
```javascript
function foo([a, b]) {
  console.log(a, b);
}

function boo({ a, b }) {
  console.log(a, b);
}

foo([1, 2]); // 1 2
boo({ a: 1, b: 2 }); // 1 2
```

### 1. 預設值
上面有提到解構賦值也能給予預設值，函式參數也能給預設值，因此必須留意兩者之間的行為差異。

```javascript
function foo({ x = 10 }) {
  console.log(x);
}

foo(123); // 10
foo(1); // 10
foo([]); // 10
foo({}); // 10
foo(); // TypeError
```
輸入的引數，沒有對應的值將會使用預設值，但如果引數是空的將會拋出錯誤。

因此可以使用參數預設值來解決，預設給予一個空物件：
```javascript
function foo({ x = 10 } = {}) {
  console.log(x);
}
foo(123); // 10
foo(1); // 10
foo([]); // 10
foo({}); // 10
foo(); // 10
```

別寫成這樣：
```javascript
function foo({ x } = { x: 10 }) {
  console.log(x);
}

foo(); // 10
```
雖然當引數沒有傳入，也會得到預設值，但完全是不同意思。
```javascript
// 承接上方程式碼

foo(123); // undefined
foo(1); // undefined
foo([]); // undefined
foo({}); // undefined
```

第一種作法為，當引數沒輸入會套用空物件，不論引數是啥，都會套用 `{ x: 10 }` 來解構。

而第二種做法則是將 `{ x: 10 }` 作為參數預設值，而非解構預設值，因此只在引數沒被輸入時適用，如果輸入的引數無法解構，都會獲得 `undefined` 的結果。

#### 1. 具名參數

有時候一個函式可能會有許多參數，舉例來說：
```javascript
function showMenu(title, width, height, items) {
  // ...
}
```
當參數變多時，要輸入引數時，就必須記住輸入順序，才能正確輸入，而且當要插入新參數，以前所呼叫的函式會變得難以維護。

因此這裡就可以用解構賦值語法來處理：
```javascript
function showMenu({
  title = 'new Title',
  width = 200,
  height = 200,
  items = [],
}) {
  //...
}

let options = {
  title: 'My menu',
  items: ['Item1', 'Item2'],
};

showMenu(options);
```

具名參數的優點：
- 參數多的情況下，也容易理解程式意義
- 方便顯示可省略參數
- 自由變更參數順序

另外要注意，定義具名參數要記得給參數預設值空物件 `{}`，否則沒輸入引數會拋出錯誤。


### 2. 其餘預算子
其餘預算子可以被解構，換句話說，可以把這個陣列解開，並將各個元素取出成為個別的變數。

```javascript
function foo(...[a, b, c]) {
  console.log(a, b, c);
}

foo(1); // 1 undefined undefined
foo(1, 2, 3); // 1 2 3
foo(1, 2, 3, 4);  // 1 2 3
```
