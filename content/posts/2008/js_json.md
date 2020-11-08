---
title: "JSON 格式"
date: 2020-08-21
description: AJAX 資料交換最為流行的資料格式。"
keywords: ["JavaScript", "JSON", "AJAX" ,"竹白"]
tags: ["JavaScript", "JSON", "AJAX"]
categories: ["JavaScript筆記"]
image: images/covers/2008/js_json.png
draft: false
---

>[JavaScript筆記 目錄](/posts/190620_javascript)

AJAX：
- [AJAX](/posts/2008/js_ajax)
- [JSON](/posts/2008/js_json)
- [XMLHttpRequest](/posts/2008/js_xhr)
- [Fetch](/posts/2008/js_fetch)

<!--more-->

{{< featuredImage >}}

## 什麼是 JSON

JSON 全稱為 Javascript Object Notation，意思就是 JavaScript 物件表示法，是資料交換的格式的一種，副檔名為 `.json`。

目前是 AJAX 資料交換最為流行的資料格式。

### 1. JSON 與程式設計語言無關

儘管 JSON 是 JavaScript 的一個子集，但 JSON 資料格式與語言無關是獨立於語言的文字格式，目前很多程式語言都支援 JSON 格式資料的生成和解析。

### 2. 優點

- 相容性高；
- 格式容易瞭解，閱讀及修改方便；
- 支援許多資料格式；
- 許多程式都支援函式庫讀取或修改 JSON 資料。

### 3. 應用

原本 AJAX（Asynchronous JavaScript and XML）技術在 JavaScript 中，主要使用的資料格式是 XML 格式，但 XML 格式很繁瑣，讓使用者撰寫不方便，而且不容易嵌入網頁中進行處理。

為了讓網頁上的共通程式語言 JavaScript 可以輕易的交換資料，目前 AJAX 交換資料都改用 JSON 格式資料。

## 語法

### 1. JSON 與 JavaScript 物件的差異

JSON 物件基本上就是 JavaScript 物件」，而這敘述在大多數情況下都對。

但有幾點必須注意：
- 複合類型的值只能為陣列或物件，不能是函式、正規表達式、日期物件等。
  - 物件屬性名稱必須加上雙引號
  - 陣列或物件最後一行不可以有逗號 `,`
- 基本型別值只能有四種：字串、數字、布林值、`null`，不包含 `undefined`。
  - 字串必須用 `""` 雙引號表示
  - `NaN`、`Infinity`、`-Infinity` 會被轉成 `null`
- 除了物件，陣列也可以是有效的 JSON 物件。

舉例來說，下面是 JavaScript 的物件實字：
```javascript
var obj = {
  name: 'Chupai',
  age: 26,
  childs: [{ name: 'Wang', age: 3 }],
};
```

若使用 JSON 表示，則是如下：
```javascript
{
  "name":"Chupai",
  "age":26,
  "childs":[{"name":"Wang","age":3}]
}
```

陣列作為 JSON 格式：
```json
[ 
  { 
    "name":"Chupai",
    "age":26,
    "childs":[ 
       { 
          "name":"Wang",
          "age":3
       }
    ]
  },
  { 
    "name":"Mike",
    "age":11,
    "childs": null
  }
]
```

### 2. JSON 檢驗、格式化工具

- [JSON Formatter](https://jsonformatter.curiousconcept.com/)
- [JSONLint](https://jsonlint.com/)


## 處理 JSON 資料

- <a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify" target="_blank">`JSON.parse()`</a>
- <a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse" target="_blank">`JSON.stringify()`</a>

### 1. 建立 JSON 字串

`JSON.stringify()` 可以將一個 JavaScript 的值，轉換為一個 JSON 字串。

語法：
```javascript
JSON.stringify(value[, replacer [, space]])
```
- `value`：JavaScript 的值
- `replacer`：
  - 若指定陣列，只會針對該陣列轉換 JSON 字串
  - 若指定函式，這個函式可接受物件的屬性名與值，可以自行決定如何轉換為 JSON 字串
- `space`：美化用，如果是 1 到 10 的數字，會自動換行並以指定數字作為縮排層次，如果指定字元，就會以指定的字元來進行縮排。
- 回傳值：JSON 字串

字串、數字、布林值、`null` 會自動轉換成對應的原始值：
```javascript
console.log(JSON.stringify('foo')); // "foo"
console.log(JSON.stringify(123)); // "123"
console.log(JSON.stringify(true)); // "true"
console.log(JSON.stringify(null)); // "null"
```

`undefined`、任意的函數以及 `symbol` 值，會被忽略：
```javascript
console.log(JSON.stringify(undefined)); // undefined
console.log(JSON.stringify(function () {})); // undefined
console.log(JSON.stringify(Symbol(''))); // undefined
```
上面的值，如果出現在陣列中會被轉成 `null`，如果是物件的屬性值該屬性會直接被忽略：
```javascript
var arry = [function () {}, undefined, Symbol('')];
var obj = {
  fn: function () {},
  un: undefined,
  sym: Symbol(''),
};

console.log(JSON.stringify(arry)); // "[null,null,null]"
console.log(JSON.stringify(obj)); // "{}"
```

接下來，我們來看 `JSON.stringify()` 的第二個參數。

如果是陣列，將會只保留陣列中的屬性：
```javascript
var obj = {
  a: 1,
  b: 2,
  c: 3,
  d: 4,
};

console.log(JSON.stringify(obj, ['a', 'b'])); // {"a":1,"b":2}
```

作為函式，會有兩個參數，分別是屬性名與屬性值，可以用來控制每個屬性，要回傳的值：
```javascript
var obj = {
  a: 1,
  b: 2,
  c: 3,
  d: 4,
};

console.log(
  JSON.stringify(obj, (key, value) => {
    if (key === 'b') {
      return value + 2;
    }
    return value;
  })
);

// {"a":1,"b":4,"c":3,"d":4}
```

最後是第三個參數，可用來縮排，如果是 1 到 10 的數字，會自動換行並以指定數字作為縮排層次，如果指定字元，就會以指定的字元來進行縮排：
```javascript
var obj = {
  a: 1,
  b: 2,
  c: 3,
  d: 4,
};

console.log(JSON.stringify(obj, null, 6));
// { 
//   "a": 1, 
//   "b": 2, 
//   "c": 3, 
//   "d": 4 
// } 
```

### 2. 解析 JSON 字串

`JSON.parse()` 可以將一個 JSON 字串轉換成 JavaScript 的值。

語法：
```javascript
JSON.parse(text[, reviver])
```
- `text`：JSON 字串
- `reviver`：函式
- 回傳值：物件

範例：
```javascript
console.log(JSON.parse('{}')); // {}
console.log(JSON.parse('true')); // true
console.log(JSON.parse('"foo"')); // "foo"
console.log(JSON.parse('[1, 5, "false"]')); // [1, 5, "false"]
console.log(JSON.parse('null')); // null

var json = '{"a":1,"b":2,"c":3,"d":4}';
console.log(JSON.parse(json)); // {a: 1, b: 2, c: 3, d: 4}
```

如果解析的字串不是合法的 JSON 格式，會拋出 SyntaxError：
```javascript
var json = '{"a":1,"b":2,}';

console.log(JSON.parse(json)); // SyntaxError
```
最後一行多了一個逗號。

第二個參數為函式，會有兩個參數，分別是屬性名與屬性值，可以用來控制每個屬性，要回傳的值：
```javascript
var json = '{"a":1,"b":2,"c":3,"d":4}';

console.log(
  JSON.parse(json, (key, value) => {
    if (key === 'a') {
      return value * 10;
    }
    return value;
  })
);

// {a: 10, b: 2, c: 3, d: 4}
```

如果該函式回傳 `undefined`，則該屬性將不會加到物件中：
```javascript
var json = '{"a":1,"b":2,"c":3,"d":4}';

console.log(
  JSON.parse(json, (key, value) => {
    if (value > 3) {
      return undefined;
    }
    return value;
  })
);

// {a: 1, b: 2, c: 3}
```