---
title: "陣列"
date: 2020-04-24
description: "幾乎所有程式語言都支援陣列，因為陣列是最簡單的資料結構。"
keywords: ["JavaScript", "陣列 Array","竹白"]
tags: ["JavaScript", "陣列 Array"]
categories: ["JavaScript筆記"]
image: images/covers/200424_js_array.png
draft: false
---

>[JavaScript筆記 目錄](/posts/190620_javascript)
<!--more-->
{{< featuredImage >}}

## 概述
在 JavaScript 中的陣列與其他語言的不同之處在於，JavaScript 的陣列只是容器。任何類型的資料，都可以放入陣列。

### 1. 陣列其實就是物件

每個陣列都會有索引值（index），是從 `0` 開始的順序整數值，但陣列其實就是擁有固定鍵名的物件。

```javascript
typeof [] // "object"`

const array = ['a', 'b', 'c'];

console.log( Object.keys(array) );  // ["0", "1", "2"]
```
使用 [`Object.keys()`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Object/keys) 傳回陣列的所有 **鍵名（key name）**。可以看到陣列的鍵名就是整數 `0`、`1`、`2`。

雖然 `typeof` 無法判斷陣列與物件，但可以利用 [`instanceof`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/instanceof) 運算子區分陣列和物件。
```javascript
const o = {};
const a = [];

console.log( o instanceof Array ); // false
console.log( a instanceof Array ); // true
```

或是使用 [`Array.isArray()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/isArray) 來判斷：
```javascript
// 承接上方程式碼

console.log( Array.isArray(o) ); // false
console.log( Array.isArray(a) ); // true
```

### 2. 為何要使用陣列？

假設需要儲存一年中每個月的平均溫度：
```javascript
let averageTep1 = 25;
let averageTep2 = 22;
let averageTep3 = 24;
let averageTep4 = 28;
let averageTep5 = 29;
...
```
使用變數來存放絕對不是一個好辦法。

因此可以使用陣列來存放資料，能夠一次管理多個變數，
```javascript
const averageTep = [];

averageTep[0] = 25;
averageTep[1] = 22;
averageTep[2] = 24;
averageTep[3] = 28;
averageTep[4] = 29;
...

console.log( averageTep ); // [25, 22, 24, 28, 29 ...]
```

還可以搭配物件使用：
```javascript
const person = [
  {name: 'Peter', sex: 'male'}, 
  {name: 'Mike', sex: 'male'}, 
  {name: 'Merry', sex: 'female'}
]

console.log( person[0].name );  // "Peter"
console.log( person[2].sex );   // "female"
```

## 基本操作
### 1. 建立 
建立陣列有兩種基本方式：
- 建構函式 `Array()`
- 陣列實值 `[]`

```javascript
const arr1 = [1, 2, 3];
const arr2 = new Array(1, 2, 3);

console.log( arr1 );  // [1, 2, 3]
console.log( arr2 );  // [1, 2, 3]
```
`new Arry()` 與 `Arry()` 同義，表示 `new` 可省略。。

但是，在 JavaScript 應該優先考慮使用 `[]` 陣列實值。除了用到字元比較少之外，還有 JavaScript 是自由度高的程式語言，`new Array()` 有可能會被複寫，創造出不是陣列的東西。

另外 `Array` 建構式有一種特殊的使用方式，如果只傳入一個數值引數被傳入，那此建構函式不會把該值當成陣列中內容，而是當作長度 `lenght`。

JavaScript 的陣列不需要預設大小，宣告後就可以新增需要的值（大部分高階語言的陣列宣告後大小就固定了）。

### 2. 取值

可以透過索引取得元素的指定值：
```javascript
const array = [1, 2, 3, 4, 5];

console.log( array[0] ); // 1
console.log( array[1] ); // 2
console.log( array[2] ); // 3
console.log( array[3] ); // 4
console.log( array[4] ); // 5
```

### 3. 陣列內容操作

除了一開始宣告就有的元素，還可以直接透過賦值方式新增陣列元素：
```javascript
const array = [];

array[0] = 1;
array[1] = 2;

console.log( array );  // [1, 2];
```
如果該索引位置已經有該元素，那將會覆蓋原來的值。

新增／刪除頭尾的元素可以使用：
- [`push()`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/push) 會添加一個或多個元素至陣列的末端，並且回傳陣列的新長度。
- [`unshift()`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/unshift) 會添加一個或多個元素至陣列的開頭，並且回傳陣列的新長度。
- [`pop()`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/pop) 會移除並回傳陣列的最後一個元素。此方法會改變陣列的長度。
- [`shift()`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/shift) 會移除並回傳陣列的第一個元素。此方法會改變陣列的長度。

以下兩個陣列方法能新增最後或最前的元素：
```javascript
// 承接上段程式碼

array.push(3);
console.log( array );  // [1, 2, 3]

array.push(4, 5, 6);
console.log( array ); // [1, 2, 3, 4, 5, 6]

array.unshift(0, 0);
console.log(array); // [0, 0, 1, 2, 3, 4, 5, 6]
```
以下兩個陣列方法能移除最後或最前的元素：
```javascript
const array = [1, 2, 3, 4];

array.pop();
console.log( array );  // [1, 2, 3]

array.shift();
console.log( array );  // [2, 3]
```

如果要刪除特定位置的元素，如果用 `deleta` 運算子將其移除，會有以下問題：
```javascript
const array = [1, 2, 3, 4];

delete array[1];

console.log( array.length );  // 4
console.log( array ); // [1, undefined, 3, 4]
```
這樣會導致陣列變成**稀疏陣列**（關於稀疏陣列將在下面說明）。

#### 3.1 特定位置插入元素、刪除特定位置元素

要在陣列中插入元素、移除特定元素，可以使用 [`splice()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)，能通過刪除或替換現有元素或者原地添加新的元素來修改陣列，並以陣列形式回傳被修改的內容。

此方法會改變原陣列。

語法：
```javascript
array.splice(start[, deleteCount[, item1[, item2[, ...]]]])
```
- `start`：指定修改的起始位置，可以是負數，由末尾計算索引。
- `deleteCount`：表示要移除的數組元素的個數，從起始位置開始刪除。
- `item1, item2, ...`：要插入的元素，由起始位置之後插入，如果不設定將只會刪除元素。

假設我們要在索引位置 2 之後插入一個新的元素：
```javascript
const array = [0, 1, 2, 3, 4, 5];

array.splice(2, 0, 'a');

console.log( array );  // [0, 1, "a", 2, 3, 4, 5]
```
起始位置設為 2，刪除個數 0。

如果我們要刪除索引位置 3 的元素：
```javascript
const array = [0, 1, 2, 3, 4, 5];

array.splice(3, 1);

console.log( array );  // [0, 1, 2, 4, 5]
```
起始位置設為 3，刪除個數 1，不設定插入元素。

#### 3.2 填充陣列

`fill()` 為 ES6 新增的方法，可以一次設定，起始位置至終止位置的值。不包含終止位置。

此方法會改變原陣列。

語法：
```javascript
array.fill(value[, start[, end]])
```
- `value` 設定值
- `start` 起始位置
- `end` 終止位置

```javascript
let arr = [0, 1, 2, 3];

console.log(arr.fill('a', 1, 3));
// [0, 'a', 'a', 3]

console.log(arr.fill('b'));
// ) ["b", "b", "b", "b"]
```

#### 3.3 複製貼上
[`copyWithin()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/copyWithin) 是 ES6 新增的方法，它能夠複製陣列中的某一段，並貼到陣列的其他位置，並取代原本的值。`copyWithin()` 並不會改變陣列的長度，如果貼上的長度會超過結尾會停止。

語法：
```javascript
array.copyWithin(target[, start[, end]])
```
- `target`：要貼上的起始位置。
- `start`：要複製的起始位置。
- `end`：要複製的結束位置，不包括這個位置元素。
- 如果是負數由結尾計算。

```javascript
console.log([1, 2, 3, 4, 5].copyWithin(2));
console.log([1, 2, 3, 4, 5].copyWithin(-2));
console.log([1, 2, 3, 4, 5].copyWithin(0, 2));
console.log([1, 2, 3, 4, 5].copyWithin(0, -2));
console.log([1, 2, 3, 4, 5].copyWithin(0, 3, 4));
console.log([1, 2, 3, 4, 5].copyWithin(-2, -3, -1));
```

### 4. 提取陣列中的元素

[`slice()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/slice) 會回傳一個新的陣列，這一對是一個由 `begin` 和 `end` 決定的原陣列的淺拷貝（包括 `begin`，不包括`end`）。

此方法原陣列不會被改變。

語法：
```javascript
array.slice([begin[, end]]);
```
- `begin`：提取起始處的索引（從 0 開始），從該索引開始提取原陣列元素。
- `end`：提取終止處的索引（從 0 開始），在該索引處結束提取原陣列元素。slice 會提取原陣列中索引從 begin 到 end 的所有元素（包含 begin，但不包含 end）。

`begin` 和 `end` 都可以是負數，在這種情況下，從末尾計算索引。

從陣列中提取索引位置 `1` 到 `2` 的元素到新陣列：
```javascript
const arr = [0, 1, 2, 3, 4];

let newArr = arr.slice(1, 3);
console.log( newArr );
```

如果要提取某位置後的所有元素，可以省略 `end`：
```javascript
const arr = [0, 1, 2, 3, 4];

let newArr = arr.slice(2);
console.log(newArr);  // [2, 3, 4]
```

因為會建立新的陣列，因此可用來淺拷貝陣列：
```javascript
const arr = [0, 1, 2, 3, 4];

let newArr = arr.slice();
console.log( newArr ); // [0, 1, 2, 3, 4]
```

如果搭配展開運算子 `...` ，可以建立出一個移除特定位置的新陣列。

假設要移除索引 `3` 的元素：
```javascript
const arr = [0, 1, 2, 3, 4, 5];

let index = 3;

let newArr = [...arr.slice(0, index), ...arr.slice(index + 1)];

console.log( newArr );  // [0, 1, 2, 4, 5]
```

### 5. 元素數量

陣列的 `length` 屬性可以查看裡面存放多少資料。

```javascript
let array = [1, 2, 3, 4, 5];

console.log(  array.length );   // 5
```
`length` 屬性可讀可寫，因此也可以將其賦值為`0`，會等同清空陣列。
```javascript
array.length = 0;

console.log( array );  // []
```
如果將 `length` 屬性賦值的數值小於元素，將會刪除後面的元素。

但這裡需要注意，如果將 `length` 給予大於其元素的數值，那會導致陣列變成**稀疏陣列**。

#### 5.1 清空 Array 的操作
常見的作法有兩種，兩種看似都是清空陣列，但並不相同：
1. `Array.length = 0`
2. `Array = []`

第一種在上面有提到過，就是將陣列元素都移除。

而第二種要注意的是，陣列跟物件一樣是傳址，每個陣列都會有自己的記憶體位址，而 `Array = [ ]` 其實是將一個新的空陣列記憶體位置傳給原本存放舊陣列的變數。

簡單來說，`Array.length = 0`，是將原本的陣列所有元素移除，而 `Array = []` 是重新給一個新陣列。

舉例說明：
```javascript
const myCart = [];
myCart.push('手機', '電視');
console.log( myCart ); // ["手機", "電視"]

const MingCart = myCart;

MingCart.push('風扇');

console.log( myCart ); // ["手機", "電視", "風扇"]
```
假設我有一個空購物車，我買了手機、電視，放進購物車。

小明也有一個購物車，而且跟我共用。

所以小明加入、刪除商品，我的購物車都會受到影響。

如果使用 `Array.length = 0`，因為是共用的購物車，所以小明的購物車也會被清空。
```javascript
// 承接第一段程式碼

myCart.length = 0;
console.log( MingCart ); // []
```
但如果使用 `Array = []`，那我會得到一個新的空購物車，而小明還是使用原本的購物車。
```javascript
// 承接第一段程式碼

myCart = [];
console.log( MingCart );  // ["手機", "電視", "風扇"]
```

### 6. 合併陣列
[`concat()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/concat) 用於合併兩個或多個陣列。此方法不會更改現有陣列，而是返回一個新陣列。

```javascript
const arr1 = ['a', 'b', 'c'];
const arr2 = ['d', 'e', 'f'];

console.log( arr1.concat(arr2) );
// ["a", "b", "c", "d", "e", "f"]
```

### 7. 字串相關方法

陣列有自己的 [`toString`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/toString) 方法，會回傳以逗號分隔的元素列表的字串：

```javascript
const arr = [1, 2, 3];

console.log( arr.toString() ); // "1,2,3"
```
其他轉字串操作的也是一樣結果：
```javascript
// 承接上方程式碼

console.log( String(arr) ); // "1,2,3"
console.log( arr + '' );  // "1,2,3"
```
因為陣列沒有 `Symbol.toPrimitive`，也沒有 `valueOf()`，它們只能執行 `toString()` 進行轉換。

[`join()` ](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/join) 能將一個陣列的所有元素連接成一個字串並回傳這個字符串。如果陣列只有一個項目，那麼將回傳該項目而不使用分隔符號。

語法：
```javascript
array.join([separator])
```
`separator` 分隔符號，預設為 `,`。

```javascript
let arr = [1, 2, 3];

console.log( arr.join() );  // "1,2,3"
console.log( arr.join('?') );  // "1?2?3"
```


### 9. 排序陣列

[`reverse()`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/reverse) 方法會原地（in place）反轉（reverses）一個陣列。陣列中的第一個元素變為最後一個，而最後一個元素則變成第一個。

```javascript
const arr = [1, 2, 3, 4];

arr.reverse();

console.log(arr); // [4, 3, 2, 1]
```

[`sort()`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)方法會原地（in place）對一個陣列的所有元素進行排序，並回傳此陣列。

語法：
```javascript
arr.sort([compareFunction])
```
- `compareFunction` 指定一個函式來定義排序順序。假如省略此參數，陣列將根據各個元素轉為字串後的每一個字元之 Unicode 編碼位置值進行排序。
- 回傳值：排序後的陣列

如果使用 `compareFunction` 函式，就要定義這個回呼函式 `compareFunction（a, b）`，讓排序演算法知道兩筆資料項之間的關係，可能的執行結果包括：
- 如果回呼函式回傳值小於 `0`，則會把 `a` 排在小於 `b` 之索引的位置，即 `a` 排在 `b` 前面。
- 如果回呼函式回傳值等於 `0`，`a` 與 `b` 皆不會改變彼此的順序，但會與其他全部的元素比較來排序。
- 如果回呼函式回傳值大於 `0`，則會把 `b` 排在小於 `a` 之索引的位置，即 `b` 排在 `a` 前面。

所以，比較函式會是以下形式：
```javascript
function compare(a, b) {
  if (在某排序標準下 a 小於 b) {
    return -1;
  }
  if (在某排序標準下 a 大於 b) {
    return 1;
  }
  // a 必須等於 b
  return 0;
}
```

如果比較數值而不是字串，比較函式可以僅僅利用 `a` 減 `b`。以下函式將會升冪排序陣列：
```javascript
function compareNumbers(a, b) {
  return a - b;
}
```
如果要降冪排序就改成 `b - a`。

範例說明：
```javascript
const inventors = [
  { name: 'Albert', year: 1879 },
  { name: 'Isaac', year: 1643 },
  { name: 'Galileo', year: 1564 },
  { name: 'Marie', year: 1867 },
  { name: 'Johannes', year: 1571 },
  { name: 'Nicolaus', year: 1473 }
];
```
排序數值大小，將資料依據出生年分排序：
```javascript
const yearSort = inventors.sort(function(a, b) {
  return a.year - b.year;
});

console.log( yearSort );
// [
//   { name: 'Nicolaus', year: 1473 },
//   { name: 'Galileo', year: 1564 },
//   { name: 'Johannes', year: 1571 },
//   { name: 'Isaac', year: 1643 },
//   { name: 'Marie', year: 1867 },
//   { name: 'Albert', year: 1879 }
// ]
```
排序字串，將資料依據字串第一個字母排序：
```javascript
inventors.sort(function(a, b) {
  
  if(a.name[0] < b.name[0]) {
    return -1;
  }
  if(a.name[0] > b.name[0]) {
    return 1;
  }
  return 0;
});

console.log( inventors );
// [
//   { name: 'Albert', year: 1879 },
//   { name: 'Galileo', year: 1564 },
//   { name: 'Isaac', year: 1643 },
//   { name: 'Johannes', year: 1571 },
//   { name: 'Marie', year: 1867 },
//   { name: 'Nicolaus', year: 1473 }
// ];
```
也可以使用 `?:` 三元條件運算子簡化：
```javascript
inventors.sort(function(a, b) {
  return (a.name[0] > b.name[0]) ? 1 : (a.name[0] < b.name[0]) ? -1 : 0
});

console.log( inventors );
// [
//   { name: 'Albert', year: 1879 },
//   { name: 'Galileo', year: 1564 },
//   { name: 'Isaac', year: 1643 },
//   { name: 'Johannes', year: 1571 },
//   { name: 'Marie', year: 1867 },
//   { name: 'Nicolaus', year: 1473 }
// ];
```

**注意事項：**
- `sort()` 不會產生新陣列，會變動原本的陣列。
- 排序不一定是穩定的，各家瀏覽器都不同，可以參考下面文章。

>[從 Array 的 sort 方法，聊到各瀏覽器的實作，沒想到 Chrome 和FireFox 的排序如此不同](https://medium.com/@realdennis/javascript-%E5%BE%9Earray%E7%9A%84sort%E6%96%B9%E6%B3%95-%E8%81%8A%E5%88%B0%E5%90%84%E5%AE%B6%E7%80%8F%E8%A6%BD%E5%99%A8%E7%9A%84%E5%AF%A6%E4%BD%9C%E7%AE%97%E6%B3%95-c23a335b1b80)

### 8. 查詢陣列
- `indexOf(item, from)`：從索引 `from` 的位置往右查詢 `item`，如果 `from` 省略則從頭開始。回傳符合的第一個元素的索引，沒有則回傳 `-1`。
- `lastIndexOf(item, from)`：和上面相同，只是往 `from` 的左邊查詢，如果省略就是從尾部開始。
- `includes(item, from)`：從索引 `from` 查詢 `item`，如果找到則回傳 `true`。

```javascript
const arr = ['a', 'c', 'c', 'a'];

console.log(arr.indexOf('c')); // 1
console.log(arr.indexOf('c', 2)); // 2

console.log(arr.lastIndexOf('c')); // 2
console.log(arr.lastIndexOf('c', 1)); // 1

console.log(arr.includes('c')); // ture
console.log(arr.includes('c', 2)); // ture
```
但要注意的是，這些方法是使用 `===` 嚴格比較。

如果要處理比較複雜的比較，可以改使用 `find()` 或 `findIndex()`。

## 陣列兩個容易誤用的重點

陣列兩個容易誤用的重點：
- 稀疏陣列（Sparse Array）
- 類陣列（Array-Like）

### 1. 稀疏陣列
稀疏陣列是指陣列中有 **插槽（slot）** 可能未定義其值或被略過而導致存放 `undefined` 的狀況，範例如下：
```javascript
const arr = [];
arr[0] = 1;

arr[2] = [ 3 ];

console.log( arr[1] ); // undefined
console.log( arr.length ); // 3
```

雖然這樣不會出錯誤，但如果不注意這種小細節，在之後的陣列操作上會出現難以發現的 BUG。 

若使用「很像數字」的字串當成鍵名來存資料，鍵值會被強制轉型為數字。
```javascript
const arr = [ ];

arr['13'] = 42;

console.log( arr.length ); // 14
```
這樣導致 `arr` 成了稀疏陣列，其長度就被誤判，因此沒事別對陣列額外新增 **鍵值對（key-value）**。若索引值非數值還是用乖乖使用物件吧。

#### 1.1 `Array.of()`

`Array()` 建構函式建立陣列，有一個常見陷阱，就是當只有一個引數傳入，並不會產生已那個數字為元素的陣列。

預期建立的陣列為 `[3]`，但實際結果為一個長度為 3 的稀疏陣列：
```javascript
const arr = Array(3);

console.log( arr[0] );  // undefined
console.log( arr.length );  // 3
```

ES6 新增了 `Array.of()` 用來取代 `Array()`，不會發上述的怪異行為：
```javascript
const arr = Array.of(3);

console.log(arr[0]); // 3
console.log(arr.length); // 1
```

不過一樣很少會用到，使用陣列實值建立陣列優先。


### 2. 類陣列（Array-Like）

類陣列（Array-Like）是具有有索引和 `length` 屬性的物件，可能各種形式的 **串列（lists）** 或字串，看起來很像陣列，但並非真正的陣列，所以沒有繼承陣列的方法。

有時候為了使用陣列的方法（例如 `map`、`indexOf` 等等方法）來處理類陣列的元素，因此會需要將其轉成真正的陣列。

舉例來說 DOM 物件操作後所得到的串列：
```javascript
const list = document.getElementsByTagName('div');
list 

console.log( list );  // HTMLCollection(3) [div, div, div]
console.log( list.length );  // 3
```
`document.getElementsByTagName` 會得到一個節點集合，並非真正的陣列，因此沒有繼承陣列的方法，因此需要將它轉成陣列，才能使用陣列的方法。

轉換串列成陣列的方法
- `Array.prototype.slice.call()`
- `Array.apply()`
- `[...]` ES6 新增
- `Array.from()` ES6 新增

```javascript
const link = document.links;

Array.isArray( link );  // false
Array.isArray( Array.prototype.slice.call(link));  // true
Array.isArray( Array.apply(null, link));  // true
Array.isArray( [...link]);  // true
Array.isArray( Array.from(link));  // true
```
