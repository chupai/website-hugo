---
title: "JavaScript 學演算法（四）- 陣列 Array "
date: 2020-04-26
description: "這週是六角鼠年鐵人賽第十二週，最基本的資料結構「陣列 Array」。"
keywords: ["演算法 Algorithm", "資料結構", "JavaScript 學演算法","竹白"]
tags: ["資料結構","陣列 Array", "w3HexSchool"]
series: ["JavaScript 學演算法"]
categories: ["w3HexSchool六角鼠年鐵人賽"]
image: images/covers/200426_ds_array.png
draft: false
---

這週是 六角鼠年鐵人賽 第十二週，要來簡單說明，最基本的資料結構「陣列 Array」。

<!--more-->
{{< featuredImage >}}

## 陣列是什麼

在計算機科學中，陣列資料結構（array data structure），簡稱 **陣列（Array）**，中國稱「数组」，是最基本的資料結構，屬於線性有序的資料結構。

大多數的高階程式語言都會內建的陣列資料型別（type），少部分是由雜湊表、連結串列、搜尋樹來實現。

### 1. 定義

陣列是由相同類型的元素所組成的有序的資料結構，通常由一塊連續的記憶體位置儲存。

因為是連續的記憶體位置，所以大部分的高階語言，陣列宣告後的長度（大小）是不能改變的，如果需要調整陣列大小，需要將所有元素搬到新的陣列。

陣列在插入、刪除元素的效率非常差，因為需要移動（往前或往後）其餘元素。

元素可以透過索引（index）做隨機存取（Random Access），有時亦稱直接存取。

![](https://i.imgur.com/AYvdlne.png)

陣列特色：
- 線性有序的資料結構
- 相同類型的元素所組成
- 由一塊連續的記憶體位置儲存
- 可以透過索引隨機存取
- 索引初始位置為 0

## JavaScript 的陣列

JavaScript 的陣列與大部分的高階語言的陣列相比，非常不一樣。

我們上面提到，陣列的定義最主要就是：
- 相同類型的元素所組成
- 由一塊連續的記憶體位置儲存

但 JavaScript 屬於於動態型別的程式語言，變數單純只是容器，型別是根據賦予的值來決定。因此陣列可以存放不同型別的元素。

JavaScript 是基於原型模式的物件導向語言，幾乎所有的事物都是物件，或是具有類似物件的行為，陣列也不例外，它本質上屬於一種特殊的物件。
```javascript
typeof [] // "object"

const array = ['a', 'b', 'c'];
console.log( Object.keys(array) );  // ["0", "1", "2"]
```

所以 JavaScript 的陣列與其它程式語言相比，執行速度較慢。

### 1. 記憶體儲存

另外，JavaScript 的陣列並不一定是儲存於連續記憶體空間。

早期的 JavaScript 引擎用為「鏈式儲存結構」，而到了 Chrome 的 V8 才使用「順序儲存結構」與「鏈式儲存結構」混合模式。

在多數的情況下，V8 引擎的陣列是「順序儲存結構」也就是分配連續記憶體，而以下情況為非連續記憶體：
- 稀疏陣列
- 元素的非單一型別

非連續記憶體的陣列用的是類似雜湊表（hash table）對映的方式存在。


## 陣列的操作

JavaScript 基本操作請參考：
- [陣列](/posts/200424_js_array)
- [迭代陣列](/posts/200425_js_array_iterate)