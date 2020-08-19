---
title: "變數"
date: 2020-08-03
description: "JavaScript 變數 var、let、const"
keywords: ["JavaScript", "變數", "let", "const", "竹白"]
tags: ["JavaScript", "ES6", "變數"]
categories: ["JavaScript筆記"]
image: images/covers/200803_js_variable.png
draft: false
---

>[JavaScript筆記 目錄](/posts/190620_javascript)

一直沒時間將之前的筆記丟到這來，剛好最近參加 [JS 學徒訓練班](/posts/2008/js60_menu/)，就趁這次機會重新整理筆記。

<!--more-->

{{< featuredImage >}}

## 變數 Variable

程式碼會需要暫時儲存一些工作所需要的資料，它可以把這些資料儲存在變數中。

變數必須先宣告才能使用它，有三種關鍵字可用來宣告：
- `var`：函式作用域
- `let`：區塊作用域、無法重複宣告
- `const`：區塊作用域、無法重複宣告、無法再次賦值


### 1. 使用 var 宣告變數

在 `ES6` 之前，`var` 是唯一可用於定義變數的關鍵字。

宣告名稱為 `a` 的變數：
```javascript
var a;

console.log(a); // undefined
```
若宣告時，未賦值，變數會被給予特殊值 `undefined`，表示值沒有被定義。


建立變數後可以使用 `=` 指定運算子來賦值。
```javascript
var a;

a = 10;
console.log(a); // 10
```

`var` 可以重複宣告：
```javascript
var a = 1;
var a = 2;

console.log(a);  // 2
```
雖然語法合法，但是不建議這麼做。

在全域範圍宣告變數，變數會成為全域物件：
```javascript
var a = 10;
console.log(this.a);  // 10
```

### 2. 變數的資料型別

JavaScript 屬於動態定型語言，變數本身並沒有型別資訊，因此建立變數時，不需要宣告型別，同一個變數可以指定不同的資料型別。


### 3. 同一個述句中宣告多個變數

```javascript
var a, b;
```

### 4. 沒宣告的變數

不需告變數也能使用：
```javascript
a = 10;
console.log(a); // 10
```
`a` 會變成全域變數，這是 JavaScript 的雷點之一。

但在 ES5 之後，新增了嚴格模式，在嚴格模式之下，變數一定要宣告，否則會拋出錯誤。
```javascript
'use strict' 

a = 10;
console.log(a);

// Uncaught ReferenceError: msg is not defined
```

### 5. 還沒宣告的變數

在 `var` 宣告變數前呼叫，也不會報錯：
```javascript
console.log(a); // undefined
var a = 10;
console.log(a); // 10
```
這是因為變數的 <a href="https://developer.mozilla.org/zh-TW/docs/Glossary/Hoisting" target="_blank">提升（Hoisting）</a> 行為。


### 6. 作用域 Scope

#### 5.1 函式作用域

使用 `var` 宣告變數將屬於當前的函式作用域，如果聲明位於任何函式外部的頂層，它就屬於全局作用域也就是全域變數。

```javascript
function num() {
  var a = 5;
}

console.log(a); // a is not defined
```

#### 5.2 巢狀作用域

當你宣告了一個變數時，它就在這個作用域內的任何地方都是可用的，包括任何下層/內部作用域。

```javascript
function foo() {
  var a = 1;

  function bar() {
    var b = 2;

    function baz() {
      var c = 3;

      console.log(a, b, c); // 1 2 3
    }

    baz();
    console.log(a, b); // 1 2
  }

  bar();
  console.log(a); // 1
}

foo();
```

### 7. 命名規則

變數命名規則，必須遵守以下條件：
1. 不能以數字起始
2. 不能使用關鍵字、或是保留字
3. 任何 Unicode 都可以拿來當作 JavaScript 的變數

但這裡有一點要注意，`undefined` 在 JavaScript 並不是保留字：
```javascript
function foo() {
  var undefined = 10;
  return undefined;
}

console.log(foo()); // 10
```
在函式中，`undefined` 可以作為變數。


## let、const

在 ES6 新增了兩種變數宣告方式。`let` 與 `const` 都是以區塊為作用域的宣告，而且不可以在同一個作用域下重複宣告，另外在全域範圍宣告變數，變數並不會成為全域物件。

兩者差異在於 `const` 是**常數**為不能再次指定值。

### 1. 以區塊為作用域的宣告

JavaScript 的變數作用域的基本單位永遠都是函式。如果需要建立一個區塊作用域，最常使用的方式就是使用立即函式運算式。
```javascript
var a = 2;

(function(){
  var a = 3;
  console.log( a );  // 3
})();

console.log( a );  // 2
```

而 `let` 或 `const` 只需要一對 `{}` 就能建立一個作用域。
```javascript
let a = 2;

{ let a = 3;
  console.log( a );  // 3
}

console.log( a );  // 2
```

### 2. let

`let` 最常使用的情境就是 `if` 迴圈，我們可以將變數的作用域限制於迴圈中。`for` 圓括號中的 `let` 變數仍然是在區塊作用域。

舉例來說，如果使用 `var` 定義 `i`：
```javascript
for(var i = 0; i < 3; i++) {
  console.log(i);
}

console.log(i); // 3
```
你可以發現 `i` 汙染了全域。

```javascript
for(let i = 0; i < 3; i++) {
  console.log(i);
}

console.log(i); // Uncaught ReferenceError: i is not defined
```

`for` 迴圈中的 `let` 變數會作重新綁定。

來看一個經典的範例：
```javascript
for(var i = 0; i < 3; i++) {
  setTimeout(function() {
    console.log(i);
  }, i * 1000);
}

// 3
// 3
// 3
```
因為 `var` 會直接將 `i` 宣告成全域變數，不斷透過 `for` 迴圈累加，在等到 `setTimeout()` 這個非同步程式實際執行時，只會拿到累加後的數值。
```javascript
for(let i = 0; i < 3; i++) {
  setTimeout(function() {
    console.log(i);
  }, i * 1000);
}

// 0
// 1
// 2
```

### 3. const

常數 constant 指是的數值不變的值。早期只有 `var` 時，宣告常數時，都是利用名稱全大寫來區分，但它還是能夠被更改。

而使用 `const` 宣告變數，有一的特點，就是 **「不能再次指定值」**，宣告時一定要賦值。

如果試著對 `const` 重新賦值，那將會報錯：
```javascript
const a = 1;

a = 2;  // Uncaught TypeError: Assignment to constant variable.
console.log( 1 );  // 2
```

但如果一開始宣告的變數內容為物件型別，那其內容屬性還是可以被改變：
```javascript
const obj = {};

obj.a = 'aa';
```
你無法更改的只是變數本身。

### 3. 重複宣告

上面提到，`var` 允許在同一個作用域下重複宣告，瀏覽器不會報錯，但 `let` 與 `const` 會出錯。

```javascript
let a;
let a = 0;
// Uncaught SyntaxError: Identifier 'a' has already been declared
```

### 4. 還沒宣告的變數

上面提到，`var` 在其宣告的前後範圍都是有效的，是因為有 **提升**。

但大部分的情況下，同常不應該有提升行為的出現，我們來看 `let` 的情況：
```javascript
console.log(a);
// Uncaught ReferenceError: Cannot access 'a' before initialization
let a = 10;
```
`let` 在正式值行前，變數不會賦值，因在沒值的狀況下被存取就會拋出錯誤。



## 撰寫風格

參考以下常見 ESLint 規範：
- <a href="https://github.com/airbnb/javascript#references" target="_blank">Airbnb JavaScript Style Guide</a>
- <a href="https://google.github.io/styleguide/jsguide.html#features-use-const-and-let" target="_blank">Google JavaScript Style Guide</a>

總結幾點建議：
- `let` 可完全替代 `var`，不應該再繼續使用 `var` 宣告變數；
- 優先使用 `const`，除非需要再指定值才用 `let`；
- 不使用逗號 `,` 將變數值宣告在同一行；
- 不要將變數都放在區塊的最上層，而是在合理位置，在首次被使用的上面一行來宣告變數。



