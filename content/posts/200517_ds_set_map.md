---
title: "JavaScript 學演算法（七）- 集合 & 映射"
date: 2020-05-17
description: "這週是六角鼠年鐵人賽第十五週，這週來看具有不重複元素特性的資料結構，「集合」與「 映射」。"
keywords: ["演算法 Algorithm", "資料結構", "JavaScript 學演算法","竹白"]
tags: ["資料結構","集合","映射", "w3HexSchool"]
series: ["JavaScript 學演算法"]
categories: ["w3HexSchool六角鼠年鐵人賽"]
image: images/covers/2005017_ds_set_map.png
draft: false
---

這週是 六角鼠年鐵人賽 第十五週，這週來看具有不重父元素特性的資料結構，「集合」 &「 映射」。
<!--more-->
{{< featuredImage >}}

## 集合（Set）

**集合**（**Set**） 是一組無順序且唯一的資料所組成的資料結構，概念衍生自數學的「**[集合](https://zh.wikipedia.org/wiki/%E9%9B%86%E5%90%88_(%E6%95%B0%E5%AD%A6))**」。

特性：
- 無序性：集合內各元素無特定排序或排序不重要。
- 互異性：集合內每個元素且只能出現一次。
- 確定性：給定一個集合，任給一個元素，該元素或者屬於或者不屬於該集合，二者必居其一。

基本操作：
- 檢查某元素是否在集合內
- 新增元素
- 刪除元素。

集合的基本操作，沒有取得指定元素的操作，這是因為集合是無序的資料結構，沒有索引或鍵名可以快速取得元素，必須透過迭代來查詢。

運算：
- 聯集：將兩個集合的元素合併成一個新集合（元素不重複）。
- 交集：將兩個集合中共有的元素，組成一個新集合。
- 對稱差：將兩個集合中不重複的元素，組成一個新集合。
- 差集：給定兩集合，回傳一個包含存在第一個集合元素但不存在於第二集合的集合

![](https://i.imgur.com/4vAQq0G.png)

## JavaScript 實作集合

JavaScript 在 ES6 就新增了 Set 物件，但我們這裡嘗試使用物件模擬簡易的 Set。

### 1. 建立類別

```javascript
class MySet {
  constructor() {
    this.items = {};
  }
  // methods
}
```
裝資料的容器 `items` 使用物件而非陣列，是因為 JavaScript 的物件屬性不會重複，可以確保集合內的元素都是唯一的。

### 2. 方法

基本操作：
- `add(element)`：新增元素。
- `delete(element)`：移除元素。
- `has(element)`：檢查元素是否存在。

因為 `add` 和 `delete` 方法會用到 `has` 方法，所以先來實現 `has` 方法。可以使用 [`in`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/in) 關係運算子，來判斷元素是否是 `items` 物件的屬性：
```javascript
has(element) {
  return element in this.items;
}
```

或是使用 [`hasOwnProperty()`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty) 來判斷物件是否有該屬性：
```javascript
has(element) {
  return this.items.hasOwnProperty(element);
}
```

如果你有使用 ESLint 會拋出 **[錯誤](https://cn.eslint.org/docs/rules/no-prototype-builtins)**，可以改成 `Object.prototype.hasOwnProperty.call(this.items, element)`。

新增元素 `add` 方法，必須先檢查元素是否存在，如果元素已存在就跳出：
```javascript
add(element) {
  if (this.has(element)) return;
  this.items[element] = element;
}
```

刪除元素 `delete` 方法，也要檢查元素是否存在，如果元素已存在就刪除：
```javascript
delete(element) {
  if (this.has(element)) {
    delete this.items[element];
  }
}
```

其餘輔助屬性方法：
- `clear()`：清空。
- `size()`：元素數量。
- `values()`：回傳一個包含所有元素的陣列。

最終程式碼：
```javascript
class MySet {
  constructor() {
    this.items = {};
  }

  has(element) {
    return Object.prototype.hasOwnProperty.call(this.items, element);
  }

  add(element) {
    if (this.has(element)) return;
    this.items[element] = element;
  }

  delete(element) {
    if (this.has(element)) {
      delete this.items[element];
    }
  }

  clear() {
    this.items = {};
  }

  size() {
    return Object.keys(this.items).length;
  }

  values() {
    return Object.values(this.items);
  }
}
```

建立實體：
```javascript
const set = new MySet();

set.add(1);
set.add(2);
set.add(3);
console.log(set.values()); // [1, 2, 3]

set.delete(1);
console.log(set.values()); // [2, 3]
```

因為是簡易的 Set，所以有很多問題，例如：物件的屬性只能儲存字串，如果元素值為其他的型別都會被強制轉型成字串。

### 3. ES6 原生 Set 

Set 只能使用建構式建立：
```javascript
new Set([iterable]);
```
能夠接受一個參數 `iterable`（可迭代物件）。

```javascript
const set1 = new Set(['a', 'b', 'c']);
console.log(set1); // Set {'a', 'b', 'c'}

const set2 = new Set('Hello');
console.log(set2); // Set {'H', 'e', 'l', 'o'}
```

屬性方法：
- `add()`：新增元素。
- `clear()`：移除其所有元素。
- `delete()`：移除指定元素。
- `has()`：檢查員素是否存在。
- `values()`：回傳一個 Iterator 物件，包含著 Set 物件中所有元素，由插入順序排序。
- `size`：元素數量。
- `forEach()`：迭代處理元素，用法等同陣列的 `forEach()`。

```javascript
const set = new Set();

set.add('a');
set.add('b');
set.add('c');
console.log(set); // Set {'a', 'b', 'c'}

set.delete('b');
console.log(set); 
```

>更詳細內容請參考 [MDN](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Set)。

## 集合操作

使用 ES6 原生 Set。

### 1. 聯集

創建一個函式，回傳兩個集合中所有元素的新集合。

最基本的作法就是迭代集合，將元素新增至新集合中：
```javascript
function uion(set1, set2) {
  const newSet = new Set();
  set1.forEach(item => newSet.add(item) );
  set2.forEach(item => newSet.add(item) );
  return newSet;
}
```

更簡單的的作法就是，使用展開運算子（spread operator），將兩個集合在一個陣列中展開，並建立新集合。
```javascript
function union(set1, set2) {
  return new Set([...set1, ...set2]);
}
```

```javascript
const set1 = new Set(['a', 'b', 'c']);
const set2 = new Set(['a', 'c', 'e']);

console.log(union(set1, set2)); // Set {'a', 'b', 'c', 'e'}
```


### 2. 交集

將兩個集合中共有的元素，組成一個新集合。

```javascript
function intersection(set1, set2) {
  const temp = new Set();
  set1.forEach(item => {
    if(set2.has(item)) {
      temp.add(item);
    }
  });
  return temp;
}
```
使用 `has()` 去檢查元素。

```javascript
const set1 = new Set(['a', 'b', 'c']);
const set2 = new Set(['a', 'c', 'e']);

console.log(intersection(set1, set2));  // Set {'a', 'c'}
```

### 3. 對稱差

將兩個集合中不重複的元素，組成一個新集合。

```javascript
function difference(set1, set2) {
  const temp = union(set1, set2);
  const intersectionSet = intersection(set1, set2);
  
  intersection.forEach(item => {
    if(temp.has(item)) {
      temp.delete(item);
    }
  });
  return temp;
}
```
簡單來說就是聯集減去交集。

```javascript
const set1 = new Set(['a', 'b', 'c']);
const set2 = new Set(['a', 'c', 'e']);

console.log(difference(set1, set2)); // Set {'b', 'e'}
```

### 4. 差集

給定兩集合，回傳一個包含存在第一個集合元素但不存在於第二集合的集合。

```javascript
function subtracting(set1, set2) {
  const temp = new Set(set1);
  set2.forEach((item) => {
    if (temp.has(item)) {
      temp.delete(item);
    }
  });
  return temp;
}
```
先複製 `set1` 再減去 `set2` 擁有的元素。

或者是新建一個集合，再將沒重複的元素新增至新集合中：
```javascript
function subtracting(set1, set2) {
  const temp = new Set();
  set2.forEach((item) => {
    if (!temp.has(item)) {
      temp.add(item);
    }
  });
  return temp;
}
```

```javascript
const set1 = new Set(['a', 'b', 'c']);
const set2 = new Set(['a', 'c', 'e']);

console.log(subtracting(set1, set2)); // Set {'b'}
```


### 5. 子集

檢查該集合是是否為另一個集合的子集。

```javascript
function subSet(set1, set2) {
  if (set1.size > set2.size) return false;

  for (let item of set1) {
    if (!set2.has(item)) return false;
  }
  return true;
}
```

`forEach` 沒辦法 `return` 所以我們使用 `for of` 來迭代 Set。

```javascript
console.log(subSet(new Set(['a', 'b', 'c']), new Set(['a', 'c', 'e'])));
// false

console.log(subSet(new Set(['a', 'b', 'c']), new Set(['a', 'b', 'c', 'e'])));
// true
```

## 映射（Map）

**映射**（**Map**）或稱 **關聯陣列**（**Associative Array**）、**字典**（**Dictionary**）是一種以「鍵值對 `{key: value}` 」形式儲存的有序資料結構，會根據不同語言有不同的名稱。

映射，就如同電話簿中的名字和號碼一樣，先找到名字，就知道它的電話號碼。鍵（key）就是姓名，而值（value）如同電話號碼。

定義：
- `key` 一定對映 `value`
- 一個映射不能包含重複的 `key`
- 每個 `key` 最多只能對映到一個 `value`

基本操作：
- 新增
- 刪除
- 查詢

## JavaScript 中的映射

JavaScript 的物件物件本質上是鍵值對的資料結構，當你需要將 `key` 對應到 `value` 時，可以將字串作為 `key` 對應到任何型別的 `value`，搭配 `in`、`delete`、`[]` 等，能達到映射的相關操作。

然而，物件終究不是映射類型，可能會遇到以下問題：
- 因為物件原型的特性，因此可能對應到意外的東西。
- 不易知道物件裡有多少對應。
- 物件無法使用非字串值作為 `key`。
- 無法保證 `key` 的順序。

請考慮以下情況，物件無法使用非字串值作為屬性名：
```javascript
const m = {};

const x = { id: 1 };
const y = { id: 1 };

m[x] = 'foo';
m[y] = 'bar';

console.log(m[x]); // bar
```
你可以發現 `{ id: 1 }` 被強制轉型成字串，`m[x]` 與 `m[y]` 指向同一個屬性。

### 1. ES6 原生 Map 物件

JavaScript 在 ES6 實現了 Map 物件，就是字典。

Map 只能使用建構式建立：
```javascript
new Map([iterable]);
```
`iterable` 基本上為陣列（或其他元素成鍵值對的可迭代物件）。

用法如下：
```javascript
const m = new Map([
  ['a', 1],
  ['b', 2],
]);

console.log(m); // Map { 'a' => 1, 'b' => 2 }
```
陣列的第一個項目就 `key`，第二個為 `value`。

或是使用 [`Object.entries`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/entries) 方法，它回傳的格式就如同上面所需要的：
```javascript
const obj = { a: 1, b: 2 };
const iterable = Object.entries(obj);
console.log(iterable); // [ [ 'a', 1 ], [ 'b', 2 ] ]

const m = new Map(iterable);
console.log(m); // Map { 'a' => 1, 'b' => 2 }
```
也就是說可以使用此方法將物件轉成 Map。

存取元素的方法為：
- `set(key, value)`：根據 `key` 存儲 `value`。
- `get(key)`：根據 `key` 回傳 `value`，如果 map 中該 `key` 不存在，回傳 `undefined`。

```javascript
const m = new Map();

m.set('a', 1);

console.log( m.get('a') ); // 1
console.log( m.get('b') ); // undefined
```

`set()` 會回傳當前 `map` 實體，因此可以採鏈式寫法：
```javascript
const m = new Map();

m.set('a', 1)
  .set('b', 2)
  .set('c', 3);
```

移除元素需要使用 `delete(key)`，而不是 `delete` 運算子，而 `has(key)` 可以判斷 `key` 是否存在。
```javascript
const m = new Map([
  ['a', 1],
  ['b', 2]
]);

console.log( m.has('a') );  // true
m.delete('a');
console.log( m.has('a') );  // false
```

Map 與物件另一個最大差異就是有 `forEach` 方法和可以使用 `for of` 迭代：
```javascript
const m = new Map([
  ['a', 1],
  ['b', 2],
  ['c', 3],
]);

m.forEach((value, key) => {
  console.log(value, key);
});
// 1 'a'
// 2 'b'
// 3 'c'

for (let key of m) {
  console.log(key);
}
// ['a', 1]
// ['b', 2]
// ['c', 3]
```

>更詳細內容請參考 [Map](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Map)。

### 2. WeakMap

除了 Map，ES6 還新增了 WeakMap。

WeakMap 與 Map 基本上一樣，除了以下幾點：
- WeakMap 的 `key` 只接受物件作為 `key` 。
- WeakMap 的 `key` 所指向的物件可以被垃圾回收。
- WeakMap 無法被迭代或清除。

```javascript
const wm = new WeakMap();

const obj = {};

wm.set(obj, 1); // 正常
wm.set('a', 2); // Uncaught TypeError Invalid value used as weak map key
```

WeakMap 只有以下屬性方法：
- `get(key)`
- `set(key, value)`
- `delete(key)`
- `has(key)`

`size`、`values()`、`entries()`、`forEach()` 等皆沒有。

WeakMap 對 `key` 引用是「弱引用」，這意味著若沒有其他引用存在時，垃圾回收機制就會釋放該物件所佔用的記憶體空間。因此 WeakMap 被設計成無法迭代。


舉例來說，我們使用一個 Map 來記錄用戶的訪問次數：
```javascript
let john = { name: 'John' };
const visitsCountMap = new Map();

visitsCountMap.set(john, 1);
```

某天這個用戶都不會來了，所以我們不需要它的訪問資料了，我們如果只有移除 `john` 的指向，`map` 內的內容還是會存在：
```javascript
john = null;

console.log( visitsCountMap.size );  // 1
```
因為還需要清除 map 的內容，所以不能先移除 `john` 的指向，需要改成先移除 `map` 內的引用，再移除 `john` 的指向：
```javascript
// 上段程式碼改成這樣

visitsCountMap.delete( john );
console.log( visitsCountMap.size ); // 0

// 清除 map 的內容再移除指向
john = null;
```

但這樣的資料移除非常麻煩，因此就有了 WeakMap：
```javascript
let john = { name: 'John' };
const visitsCountWeakMap = new WeakMap();

visitsCountWeakMap.set(john, 1);
john = null;
```
`{ name: 'John' }` 這個物件，除了 WeakMap 沒有其他引用了，所以這個物件會自動的從記憶體和 `visitsCountWeakMap` 中刪除。

>更詳細內容請參考 [WeakMap](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WeakMap)。


