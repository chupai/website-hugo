---
title: "JavaScript 註解 & 型別檢查"
date: 2021-02-10
description: "剪貼簿複製操作。"
keywords: ["JavaScript", "註解", "JSDoc", "型別檢查"]
tags: ["JavaScript", "註解", "JSDoc", "型別檢查"]
categories: ["JavaScript筆記"]
image: images/covers/2102/comments.png
draft: false
---

>[JavaScript筆記 目錄](/posts/190620_javascript)

<!--more-->

{{< featuredImage >}}

## JavaScrit 註解

### 1. 格式

JavaScrit 中的註解有兩種格式。

單行註解：
```javascript
// Hello
```

多行註解：
```javascript
/*
 Hello
 Hi
*/
```

### 2. 為什麼要加註解？

除非你的記得你幾個月前寫的程式，或能立刻明白他人寫的程式，否則良好的註解是必須的。

註解功能：
- 阻止程式碼執行。
- 用於說明程式碼，提升可讀性，方便專案管理、交接，提升協作效率。

### 3. 不必要的註解

1. 只對商業邏輯複雜的部分撰寫註解
2. 不要在程式碼中保留被註解掉的程式碼
3. 不要留有日誌式的註解
4. 避免位置標明

>[無瑕的程式碼 JavaScript - 註解（Comments）](https://github.com/AllJointTW/clean-code-javascript?fbclid=IwAR2KX561jSLRfhAqT9zMLkfM1IvvzP3k4iWzFp6c174eJSdcQHBTQPOon_0#%E8%A8%BB%E8%A7%A3comments)

## 註解應用

### 1. 特殊標記註解

```javascript
// TODO: 功能未完成
// FIXME: 程式碼須修復
// XXX: 實現方式待確認
// NOTE: 功能說明
// HACK: 程式碼還有優化空間
// BUG: 程式碼有 BUG
```

### 2. JSDoc

[JSDoc](https://jsdoc.app/index.html) 是最通用的 JavaScript 註解規範，透過特定格式的註解，可以快速建立 API 文件。

## 型別檢查

>動態型別一時爽，程式碼重構火葬場

由於 Javascript 是動態型別語言，變數型別的寬容使得程式撰寫上有不錯的靈活性，但是當專案變得龐大時，反而會使開發成本提高。

使用 [TypeScript](https://www.typescriptlang.org/) 能有效解決動態型別的缺點，但考慮到學習成本與舊專案的重構，可試試 VSCode 中基於 TypeScript 提供對於 JSDoc 支持，實現智能型別檢查。

### 1. 開啟型別檢查

最簡單的方法就是在 `.js` 檔案開頭新增 `@ts-check`：
```javascript
// @ts-check

```
那麼 VSCode 就會根據註解檢查型別。

如果不想每隻 `.js` 檔案都加上 `@ts-check`，可以開啟 VSCode 全域型別檢查：

```json
{
  "js/ts.implicitProjectConfig.checkJs": true
}
```

預設的情況下是關閉的：

![](https://i.imgur.com/ZC2ooil.png)

如果型別檢查為開啟，則可以使用 `@ts-nocheck` 忽略某隻檔案的類型檢查：
```javascript
// @ts-nocheck

```

### 2. jsconfig.json

除了啟用 VSCode 的設定來開啟型別檢查，也可以在專案根目錄下新增 `jsconfig.json` 設定檔：
```json
{
  "compilerOptions": {
    "checkJs": true
  }
}
```
`jsconfig.json` 會覆蓋 Implicit Project Config: Check JS 設定。

預設會全域檢查所有 `.js` 檔案，可以使用 `exclude` 或 `include` 選項，設定要排除或包含的資料夾：
```json
{
  "compilerOptions": {
    "checkJs": true
  },
  "exclude": ["node_modules", "dist"],
  "include": ["src/**/*"]
}
```

>[Visual Studio Code - jsconfig.json](https://code.visualstudio.com/docs/languages/jsconfig)
>[TypeScript - What is a tsconfig.json](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html#using-tsconfigjson-or-jsconfigjson)


### 3. 支持的 JSDoc 標籤

TypeScript 只支持了部分 JSDoc 標籤。

當前所支持的標籤如下：
- `@type`
- `@param` (or `@arg` or `@argument)`
- `@returns` (or `@return)`
- `@typedef`
- `@callback`
- `@template`
- `@class` (or `@constructor)`
- `@this`
- `@extends` (or `@augments)`
- `@enum`

>[JSDoc Reference](https://www.typescriptlang.org/docs/handbook/jsdoc-supported-types.html)
>[中文翻譯](https://www.tslang.cn/docs/handbook/type-checking-javascript-files.html)

### 3. 簡單範例

JSDoc 註解格式必須以 `/**` 為開頭才能被識別（例如 `/*`、`/***` 不會被解析為 JSDoc 註解）：
```javascript
/** */
```

舉例來說，我們定義一個 `count` 變數：
```javascript
let count;
```

我們可以指定任何型別的值給它，但如果希望它只接受數字型別，可以加上 JSDoc 註解：
```javascript
/** @type {number} */
let count;
```
若指定非數字型別的值，VSCode 將會跳出錯誤提示：

![](https://i.imgur.com/jgl9we4.png)

可以在註解第一行加上說明：
```javascript
/**
 * 計數變數
 * @type {number}
 * */
let count;
```

![](https://i.imgur.com/MjDaVpn.png)

如果想要忽略類型錯誤可以使用 `@ts-ignore`。

![](https://i.imgur.com/yNvJHTL.png)

## JSDoc 標籤

### 1. @type

`@type` 可以用來標明變數型別。

基本用法：
```javascript
/** @type {string} */
let str;
str = 'Hello!';

/** @type {Date} */
let now;
now = new Date();

/** @type {HTMLElement} */
let dom;
dom = document.querySelector('body');
```

複合型別：
```javascript
/** @type {string|boolean} */
let x;
x = '123';
x = true;
```

指定陣列元素的型別：
```javascript
/** @type {number[]} */
const ns = [];
[].push(1);
```

![](https://i.imgur.com/dSELgA6.png)

也可以寫成 `Array.<number>`、`Array<number>`：
```javascript
/** @type {Array.<number>} */
const ns = [];

/** @type {Array<number>} */
const ns2 = [];
```

物件字面值：
```javascript
/**
 * @type {{ a: string, b: number }}
 */
let obj;
obj = { a: '123', b: 123 };
```

指定 `map-like` 和 `array-like` 的物件：
```javascript
/**
 * @type {Object.<string, number>}
 */
let stringToNumber;

/** @type {Object.<number, object>} */
let arrayLike;
```

預設就是 `any` 任意型別：
```javascript
/** @type {any} */
let x;
```
`*` 和 `?` 等同 `any`：
```javascript
/** @type {*} - can be 'any' type*/
let y;

/** @type {?} - unknown type*/
let z;
```

函式型別：
```javascript
/**
 * @type {function(number): number} Closure syntax
 */
let foo;
foo = function (a) {
  return a * a;
};

/** 
 * @type {(a: number) => number} Typescript syntax
 */
let boo;
boo = function (a) {
  return a * a;
};
```


### 2. @param（synonyms: @arg or @argument）

`@param` 語法和 `@type` 基本上相同，但用於標明函式參數，所以多了參數名稱。

```javascript
/**
 * The square of a number
 * @param {number} number - 輸入數字
 * @return {number}
 */
function square(number) {
  return number * number;
}
```
函式如果有回傳值，則可以使用 `@returns`（`@return`） 標明。

有屬性的參數，使用物件字面值不易描述屬性：
```javascript
/**
 * @param {{ name: string, age: number }} person
 */
function foo(person) {
  console.log(person.name, person.age);
}
```

可以使用以下寫法：
```javascript
/**
 * @param {Object} person - 某人
 * @param {string} person.name - 某人的名字
 * @param {number} person.age - 某人的年齡
 */
function foo(person) {
  console.log(person.name, person.age);
}
```

使用 ES6 參數解構，使用適當的參數名稱即可：
```javascript
/**
 * @param {Object} person - 某人
 * @param {string} person.name - 名字
 * @param {number} person.age - 年齡
 */
function foo({ name, age }) {
  console.log(name, age);
}
```

可選參數表示方式：
```javascript
/**
 * @param {string=} p1 - 可選參數（Closure語法）
 * @param {string} [p2] - 可選參數（JSDoc語法）
 * @param {string} [p3 = 'test'] - 有預設值的可選參數（JSDoc語法）
 */
function foo(p1, p2, p3 = 'test') {}
```
### 3. @typedef

`@typedef` 可以用來宣告複雜型別，也就是自訂義一個類型，再使用 `@type` 標記來引用。

描述一個物件型別：
```javascript
/**
 * @typedef {Object} SpecialType - creates a new type named 'SpecialType'
 * @property {string} prop1 - a string property
 * @property {number} prop2 - a number property
 * @prop {number} [prop3] - an optional number property of SpecialType
 */

/** @type {SpecialType} */
let obj;
obj = { prop1: '123', prop2: 123 };
```

### 4. @callback

`@callback` 與 `@typedef` 相似，但描述的是一個函式：
```javascript
/**
 * @callback Predicate
 * @param {string} data
 * @returns {boolean}
 */

/** @type {Predicate} */
const foo = function (str) {
  return !(str.length % 2);
};
```

### 5. @class（synonyms: @constructor）

`@class` 可以標明函式為一個**建構函式（Constructor）**。

```javascript
/**
 * Creates a new Person.
 * @class
 */
function Person() {}

const p = new Person();
```

不過 ES6 有了 [`class`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/class) 後，就沒必要使用 `@class` 了。

```javascript
class Person {
  /**
   * Creates a new Person.
   */
  constructor() {}
}
```

### 6. @this

`@this` 可以明確標示 `this` 關鍵字在這裡指的是什麼。

例如建構函式的方法：
```javascript
/**
 * @class
 */
function Person() {
  this.name = '';
}

/**
 * @this {Person} - Person 實體
 */
Person.prototype.setName = function (name) {
  this.name = name;
};
```

或監聽器處理函式：
```javascript
/**
 * @param {Event} event - 事件物件
 * @this {HTMLElement} - 監聽器綁定元素
 */
function clickHandler(event) {
  // ...
}
```

### 7. @extends（synonyms: @augments）

如果使用 `extends` 關鍵字來擴展一個現有的類別的時候，可以使用 `@extends` 標示。

```javascript
/** 佇列 */
class Queue {}

/**
 * 優先佇列
 * @extends Queue
 */
class PriorityQueue extends Queue {}
```

### 8. @enum

`@enum` 標籤描述一個靜態屬性值的全部相同的集合，簡單來說就是一個物件內的屬性皆為相同型別，且不允許新增額外屬性。

```javascript
/** @enum {number} */
const JSDocState = {
  BeginningOfLine: 0,
  SawAsterisk: 1,
  SavingComments: 2,
};
```

### 9. @template

`@templete` 非 JSDoc 標準，只在 [google closure compiler](https://github.com/google/closure-compiler/wiki/Generic-Types) 中有提及，可以用來宣告 **泛用型別（Generic Type）**，是 TypeScript 中的型別。

**泛用型別（Generic Type）** 目的在於成員之間提供有意義的約束，這些成員可以是類別的實體、類別的方法、函式參數、函式回傳值。
 
```javascript
/**
 * @template T
 * @param {T} x
 * @return {T}
 */
function foo(x) {
  return x;
}
```
![](https://i.imgur.com/DxniJHt.png)

關於 **泛用型別（Generic Type）** 我自己也不是很了解，有空在補充。
 


## 註解相關 VSCode 套件
- [koroFileHeader](https://marketplace.visualstudio.com/items?itemName=OBKoro1.korofileheader)，在 VSCode 中用於生成檔案頭部註解和函式註解的套件。
  - 文件頭部新增註解：`Ctrl` + `Alt` + `t`
  - 光標處添加函式註解：`Ctrl` + `Alt` + `t`
- [Todo Tree](https://marketplace.visualstudio.com/items?itemName=Gruntfuggly.todo-tree)，特殊註解高光亮。
```json
// 配置
"todo-tree.general.tags": ["TODO:", "FIXME:"],
"todo-tree.highlights.defaultHighlight": {
  "gutterIcon": true,
  "foreground": "#fff",
  "type": "text",
  "opacity": 50
},
"todo-tree.highlights.customHighlight": {
  "TODO:": {
    "background": "#ffbd2a",
    "iconColour": "#ffbd2a"
  },
  "FIXME:": {
    "background": "#f06292",
    "iconColour": "#f06292",
    "icon": "flame"
  }
}
```



## 參考文獻

- [可不可以不要寫糙 code系列 第 8 篇 - 不必要的註解](https://ithelp.ithome.com.tw/articles/10204310)
- [编写高质量可维护的代码：一目了然的注释](https://zoo.team/article/the-clean-annotation)
- [導入 TypeScript 應考慮之效益與成本](https://engineering.linecorp.com/zh-hant/blog/benefits-and-costs-to-consider-when-installing-typescript/)
- [為你的 JavaScript 項目添加智能提示和類型檢查](https://fecoding.cn/2019/03/07/add-intellisense-and-type-checking-to-your-javascript-project/)
- [[Vuex]在 Vscode 中不使用 Typescript 也能讓 Vuex 讀懂變數的類型](https://medium.com/%E4%B8%80%E5%80%8B%E5%B0%8F%E5%B0%8F%E5%B7%A5%E7%A8%8B%E5%B8%AB%E7%9A%84%E9%9A%A8%E6%89%8B%E7%AD%86%E8%A8%98/vuex-%E5%9C%A8-vscode-%E4%B8%AD%E4%B8%8D%E4%BD%BF%E7%94%A8-typescript-%E4%B9%9F%E8%83%BD%E8%AE%93-vuex-%E8%AE%80%E6%87%82%E8%AE%8A%E6%95%B8%E7%9A%84%E9%A1%9E%E5%9E%8B-34c9149bca69)
- [JSDoc中文文档(@use JSDoc)](https://jsdoc.zcopy.site/)
- [基于TypeScript的JSDoc注释](https://juejin.cn/post/6844903706006126599#heading-2)
- [@ts-check 立即上手，JSDoc 添加类型](https://juejin.cn/post/6844903501936459790)
- [如何使用 JSDoc 保證你的 Javascript 型別安全性](https://www.luoow.com/dc_tw/200919357)
- [TS in JS 实践指北](https://juejin.cn/post/6844904030221631501)
- [[Web翻译]从JavaScript生成TypeScript定义文件](https://juejin.cn/post/6869264839059963911)
- [[译] 讨论 JS ⚡：文档](https://juejin.cn/post/6844903762872713224)
- [使用 JSDoc 标注类型](https://juejin.cn/post/6844903630026309646)
- [利用 VSCode、JSDoc、d.ts 让你的代码更健壮](https://juejin.cn/post/6844903646526717959#heading-5)
- [ES6写JSDoc的一些经验和实例](https://blog.csdn.net/jennieji/article/details/82587516)
- [震驚！JavaScript 竟然可以類型推斷！](https://zhuanlan.zhihu.com/p/42163515)
- ["無 Typescript" 編程](https://juejin.cn/post/6862981984801521672)
- [JSDoc support in JavaScript](https://github.com/Microsoft/TypeScript/wiki/JsDoc-support-in-JavaScript)
- [JSDoc支持_TypeScript筆記19](http://www.ayqy.net/blog/jsdoc%E6%94%AF%E6%8C%81_typescript%E7%AC%94%E8%AE%B019/)
- [Typescript 初心者手札系列 第 3 篇 - TypeScript 編譯設定 - tsconfig.json](https://ithelp.ithome.com.tw/articles/10216636)
- [vscode中的 jsconfig.json](https://zhuanlan.zhihu.com/p/55644953)
- [為什麼世界需要Typescript系列 第 7 篇 - 泛型 - 07](https://ithelp.ithome.com.tw/articles/10214253?sc=rss.qu)
- [你不知道的 TypeScript 泛型（萬字長文，建議收藏）](https://lucifer.ren/blog/2020/06/16/ts-generics/)
- [TypeScript系列（四）泛型](https://zhuanlan.zhihu.com/p/60953568)