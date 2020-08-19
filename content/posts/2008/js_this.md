---
title: "函式呼叫與 this"
date: 2020-08-04
description: "JavaScript 的大坑「this」。"
keywords: ["JavaScript", "變數", "this", "竹白"]
tags: ["JavaScript", "ES6", "變數"]
categories: ["JavaScript筆記"]
image: images/covers/200804_js_this.png
draft: false
---

>[JavaScript筆記 目錄](/posts/190620_javascript)

一直沒時間將之前的筆記丟到這來，剛好最近參加 [JS 學徒訓練班](/posts/2008/js60_menu/)，就趁這次機會重新整理筆記。

<!--more-->

{{< featuredImage >}}

## 總結

直接來看總結。

呼叫函式的方式會影響 `this` 的值：
- 直接作為函式來呼叫，通常 `this` 的值為全域物件，嚴格模式下為 `undefined`；
- 函式作為方法來呼叫，`this` 的值為被呼叫函式的所屬物件；
- 函式作為建構式來呼叫，`this` 的值為新建立的物件；
- 藉由 `apply()` 或 `call()` 呼叫，`this` 的值由第一個參數決定。

箭頭函式：
- 箭頭函式沒有自己的 `this` 的值，由建立時的環境來決定。

bind：
- 所有函式都具備 `bind()` 來建立一個新函式，此函式會綁定傳入的引數，除此之外，綁定的函式運作如原始的函式。

嚴格模式：
- 當 `this` 值為 `undefined` 或 `null` 會被強制轉成全域物件，而嚴格模式下，將不會強制轉值。


## this 的誤解

`this` 有兩個因為過度解讀字面本身的意義，而造成的誤解。

1. 第一個常見的誤解是 `this` 參考到函式本身；
2. 另一個常見的誤解是 `this` 以某種方式參考了函式自身作用域。

請問會印出啥？
```javascript
function foo() {
  console.log(this);
}

foo(); // 預期結果為 function foo() { ... }
```
結果為全域物件（瀏覽器下是 Window 物件、node.js 底下是 Global 物件）。

請問會印出啥？
```javascript
function foo() {
  var a = 2;
  this.bar();
}

function bar() {
  console.log( this.a );
}

foo(); // 預期結果為 2
```
結果為 `undefined`，因為全域物件下沒有 `a`。

## this 的指向

### 1. 一般的函式呼叫

`this` 的值與物件導向有關，一但脫離了物件，`this` 的值並不重要。

一般的函式呼叫，`this` 會指向預設的綁定（default binding），也就是全域物件（瀏覽器下是 Window 物件、node.js 底下是 Global 物件）。
```javascript
var name = 'GlobalName';

function foo() {
  var name = 'Chupai';
  console.log(this.name); 
}

foo();  // "GlobalName"
```

放到立即函式 IIFE ，直接在函式內直接在呼叫另一個函式：
```javascript
var name = 'GlobalName';

(function() {
  function foo() {
    var name = 'Chupai';
    console.log(this.name);
  }
  foo(); // "GlobalName"
})();
```
結果是一樣的。

閉包 Closure：
```javascript
var name = 'GlobalName';

function foo() {
  var name = 'Chupai';
  return function() {
    console.log(this.name);
  };
}

foo()(); // "GlobalName"
```
這樣結果依然相同。

回呼函式 Callback function：
```javascript
var name = 'GlobalName';

function foo() {
  var name = 'Chupai';
  
  function boo() {
    console.log(this.name);
  }
  
  boo();
}

foo();  // "GlobalName"
```
無論在哪一層， 一般的函式呼叫 `this` 都會指向全域物件。

為什麼 `this` 會指向全域物件，關於這部分會在下方 **[嚴格模式](#嚴格模式)** 說明。

### 2. 物件的方法呼叫

將函式綁定到一個物件屬性上，稱作方法（method）。

如果呼叫物件的方法，`this` 的值為被呼叫函式的所屬物件。

一個簡單的範例：
```javascript
var name = 'GlobalName';

var obj = {
  name: 'Chupai',
  foo: function() {
    console.log(this.name);
  },
};

obj.foo(); // "Chupai"
```
`foo` 為 `obj` 的方法，因此 `foo` 內的 `this` 會指向 `obj` 物件。

稍微改變一下程式碼：
```javascript
var name = 'GlobalName';

function foo() {
  console.log(this.name); 
}

var obj = {
  name: 'Chupai',
  foo: foo
};

foo();      // "GlobalName"
obj.foo();  // "Chupai"
```
函式宣告的位置不重要，`foo` 內的 `this` 還是會指向 `obj` 物件。

繼續看下個範例：
```javascript
var name = 'GlobalName';

function foo() {
  console.log(this.name); 
}

var obj = {
  name: 'Chupai',
  boo: {
    name: 'Wang',
    foo: foo
  }
};

obj.boo.foo(); // "Wang"
```
`foo` 為 `obj.boo` 物件的方法，所以它會指向 `obj.boo` 物件。

### 3. 間接參考

接下來看一個常見的錯誤。

如果將物件內的函式，賦予在一個變數上，並呼叫它：
```javascript
var name = 'GlobalName';

function foo() {
  console.log(this.name);
}

var obj = {
  name: 'Chupai',
  foo: foo,
};

var callThisName = obj.foo;

callThisName(); // "GlobalName"
```

當我們將 `obj.foo` 指定給變數 `callThisName` 時，會將 `foo` 函式的記憶體位置傳給 `callThisName`，因此當我們用 `callThisName()` 呼叫時，與 `obj` 無關，所以就只是一般的函式呼叫。

另外，參數傳遞中的回呼函式，也屬於間接參考：
```javascript
var name = 'GlobalName';

function foo() {
  console.log(this.name);
}

var obj = {
  name: 'Chupai',
  foo: foo,
};

function boo(fn) {
  fn();
}

boo(obj.foo); // "GlobalName"
```

### 4. 事件監聽

DOM 搭配事件監聽 `addEventListener` 時，監聽函式中的 this 會指向的則是該 DOM 物件。

```javascript
var dom = document.querySelector('body');

dom.addEventListener('click', function() {
  console.log(this);  // <body>...</body>
});
```


## 改變 this 的指向

會改變 `this` 的指向的情況：

1. 使用 `apply`、`call` 函式方法；
2. 使用 `bind` 函式方法；
3. 使用建構函式 `new` 一個物件實體；
4. 使用 ES6 的箭頭函式。

### 1. apply、call

`call()` 與 `apply()` 是能呼叫函式的方法，並且能強制指定 `this` 值：
```javascript
var obj = {};

function foo() {
  console.log(this);
}：
foo();          // "Window{}"
foo.call(obj);  // Object{}
foo.apply(obj); // Object{}
```
兩者第一個參數都是 `this` 值，也就是要綁定的物件。

而兩者差異只在於後面的參數：
```javascript
var obj = {};

function foo(a, b) {
  console.log(this, a, b);
}

foo.call(obj, 1, 2);    // Object{} 1 2
foo.apply(obj, [1, 2]); // Object{} 1 2
```
`call()` 第二個參數後，與平常呼叫函式一樣，`apply()` 則需要使用陣列將引數包起來。
 
使用 `call()` 和 `apply()` 綁定 `this` 後，再用一次就無法改變 `this` 的指向：
```javascript
function foo() {
  console.log(this.a);
}

var boo = function () {
  foo.call({ a: 2 });
};

boo(); // 2
boo.call({ a: 10 }); // 2
```

此模式稱作**硬綁定（hard binding）**，指的是綁定既明確又不會意外變回預設的綁定。

### 2. bind()

因為硬綁定模式如此常用，ES5 新增了一個方法，將此模式包裝了起來，`bind()` 會回傳一個新的函式，當被呼叫時，將提供的值設為 `this` 值。

```javascript
function foo() {
  console.log(this.a);
}

var boo = foo.bind({ a: 2 });

boo(); // 2
boo.call({ a: 10 }); // 2
```


### 3. 使用建構函式

此部分只要了解建構函式的 `this` 是指向使用 `new` 建立的物件實體本身即可。

```javascript
function Foo(a) {
  this.a = a;
}

var bar = new Foo(2);
console.log(bar.a); // 2
```

### 3. 箭頭函式

傳統函式的 `this` 是依呼叫的方法而定，因此當你的函式有好幾層時，會遇到一個問題：
```javascript
var obj = {
  a: 10,
  b: 20,
  print: function () {
    function add() {
      return this.a + this.b;
    }
    console.log(add());
  },
};

obj.print(); // NaN
```
以上範例可以看到，我們在 `print` 方法內又建立一個函式並呼叫，因為是一般呼叫，因此 `this` 會會指向全域物件，所以 `this.a` 和 `this.b` 為 undefined，因此結果為 NaN。

在 ES6 前，解決辦法是利用一個變數儲存 `this` 的值（常見命名 `_this`、`that`、`vm`、`self`）：
```javascript
var obj = {
  a: 10,
  b: 20,
  print: function () {
    var _this = this;
    function add() {
      return _this.a + _this.b;
    }
    console.log(add());
  },
};

obj.print(); // 30
```


而 ES6 新增的箭頭函式，它本身並沒有 `this`，它會在定義時記住 `this` 值，也就在宣告它的地方的 `this` 是什麼，它的 `this` 就是什麼，會根據環境來：
```javascript
var obj = {
  a: 10,
  b: 20,
  print: function () {
    let add = () => {
      return this.a + this.b;
    };
    console.log(add());
  },
};

obj.print(); // 30
```

箭頭函式注意事項：
- 箭頭函式一但綁定了 `this` 就不會再改變，使用 `apply`、`call`、`bind` 方法也無法修改；
- 箭頭函式也不能用在建構式上，會拋出錯誤；
- 用在監聽 DOM 的回呼函式上，`this` 會指向全域物件，因此要避免。

## 嚴格模式

ES5 之後，新增了嚴格模式，在嚴格模式下，一般函式呼叫的 `this` 值都是 `undefined`。

```javascript
'use strict';
function foo() {
  console.log(this);
}

foo(); // undefined
```

`undefined` 與全域物件有什麼關係？

先來一段程式碼：
```javascript
function foo() {
  console.log(this);
}

foo.call(undefined); // Window{}
foo.call(null); // Window{}
```
我們使用 `call()` 將 `this` 值設為 `undefined`，結果卻回傳全域物件。

這是因為 JavaScript 的機制，當 `this` 值為 `undefined` 或 `null` 時，會將 `this` 值強制轉換為一個物件。

在嚴格模式下，刻意將 `undefined` 或 `null` 設為 `this` 值，會回傳正確的 `this` 值。
```javascript
'use strict';
function foo() {
  console.log(this);
}

foo.call(undefined); // undefined
foo.call(null); // null
```

這就是為什麼一般函式呼叫會回傳全域物件的原因。

有的書會用「`this` 永遠指向最後呼叫它的那個物件」來解釋下面這段程式碼：
```javascript
function foo() {
  console.log(this);
}

foo(); // Window{}
window.foo(); // Window{}
```
因為 `foo()` 等同 `window.foo()`，最後呼叫它的物件是全域物件，所以 `this` 指向全域物件。但這其實是不太正確的說法，`this` 值主要還是以函式的呼叫方式為主，`foo()` 的 `this` 值會是 `undefined`，會得到全域物件是因為被強制給值了。

讓我們加上嚴格模式：
```javascript
'use strict';
function foo() {
  console.log(this);
}

foo(); // undefined
window.foo(); // Window{}
```
`window.foo()` 的值還是指向全域物件，因為它是方法呼叫。

### 1. 注意

但這裡要注意的是，呼叫地點是嚴格模式，並不會影響 this 的規則。
```javascript
function foo() {
  console.log( this.a );
}

var a = 2;

(function() {
  'use strict'
  foo();  // 2
})();
```

## 猜猜這個 this 到底是什麼？

- [JavaScript - 猜猜這個 "this" 到底是什麼？](https://www.youtube.com/watch?v=8uqUD2F_W74)
- [靠北 JS 之 This 到底是什麼辣](https://www.youtube.com/watch?v=tpheRywjVQk)


## 參考資料

書籍：
- 《忍者：JavaScript開發技巧探秘第二版》
- 《你所不知道的 JS - 範疇與 Closures、this 與物件原型》
- 《0 陷阱！0 誤解！8 天重新認識JavaScript！》
- 《JavaScript 技術手冊》

文章：
- [JavaScript This 系列文：this 為什麼指向 window](https://wcc723.github.io/javascript/2019/03/21/this-why-window/)
- [JavaScript This 系列文：this 與物件的關係
](https://wcc723.github.io/javascript/2019/03/18/JS-THIS/)