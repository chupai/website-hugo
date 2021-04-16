---
title: "物件字面值擴充功能"
date: 2020-05-15
description: "ES6 為物件字面值 {} 新增了幾個重要的方便擴充功能。"
keywords: ["JavaScript", "物件字面值擴充功能","竹白"]
tags: ["JavaScript", "物件字面值擴充功能", "ES6"]
categories: ["JavaScript筆記"]
image: images/covers/200515_js_object_literal_extension.png
draft: false
---

>[JavaScript筆記 目錄](/posts/190620_javascript)
<!--more-->
{{< featuredImage >}}

ES6 為 `{}` 物件字面值新增了幾個方便擴充功能，以下只提幾個常用。

## 簡寫

### 1. 屬性值簡寫

舉一個簡單的範例來說明。

變數與屬性名相同，這種應用場景很常見：
```javascript
function foo(a, b) {
  return { 
    a: a,
    b: b 
  };
}

console.log(foo(1, 2)); // {a: 1, b: 2}
```

但在 ES6 環境下，可以這樣寫：
```javascript
function foo(a, b) {
  return { a, b };
}

console.log(foo(1, 2)); // {a: 1, b: 2}
```
當屬性名與值名稱相同時，可以省略後面的值。

或者是：
```javascript
const foo = 'bar';

const baz = { foo };
// 等同於
const baz = { foo: foo };
```

### 2. 方法簡寫

定義方法時，都會有 `function` 宣告。
```javascript
const obj = {
  foo: function(a) {
    console.log(a);
  },
};

obj.foo(1); // 1
```

在 ES6 環境下，可以省略這個 `function` 宣告：
```javascript
const obj = {
  foo(a) {
    console.log(a);
  },
};

obj.foo(1); // 1
```

但使用簡寫的方式，函式為不具名，而且無法使用箭頭函式。

## 屬性名稱

在 ES6 新增了可計算的功能，可以利用 `[]` 包裹運算式，當作鍵名。

```javascript
let prefix = 'foo';

let myObject = {
  [prefix + 'bar']: 'hello',
  [prefix + 'baz']: 'world'
};

myObject.foobar;      // hello
myObject['foobaz'];   // world
```