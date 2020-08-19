---
title: "JavaScript 學演算法（二十）- 搜尋演算法"
date: 2020-08-10
description: "搜尋的操作是非常經典的演算法，身為資訊工程師，能否在最短時間內搜尋到所需要的資料，一直以來都是最關心的議題。"
keywords: ["演算法 Algorithm", "搜尋演算法", "竹白"]
tags: ["搜尋演算法", "w3HexSchool"]
series: ["JavaScript 學演算法"]
categories: ["w3HexSchool六角鼠年鐵人賽"]
image: images/covers/200810_search_algorithm.png
draft: false
libraries:
- katex
---

這週是六角鼠年鐵人賽第二十八週。

搜尋的操作是非常經典的演算法，身為資訊工程師，能否在最短時間內搜尋到所需要的資料，一直以來都是最關心的議題。

<!--more-->

{{< featuredImage >}}

## 什麼是搜尋（Search）

**搜尋（Search）**，簡單來說就是在一堆資料中找出特定的資料輸出，核心的操作為「比較」，只有透過比較才能判斷是否符合輸出條件。

根據資料量的大小，搜尋可分為：
1. 內部搜尋：資料量小，可從主記憶儲存資料檔中進行尋找所要的資料之操作；
2. 外部搜尋：資料量非常大，必須從外部記憶中尋找想要的資訊。因外部記憶體的操作較費時間，故需考慮如何減少存取時間與次數。

根據不同的資料結構，會有不同合適的搜尋演算法。搜尋演算法也非常的多，因為會根據需求的不同，而有很多不同的變化。

以下會說明幾個基礎的搜尋演算法：
- 線性搜尋（Linear Search）
- 二分搜尋（Binary Search）
- 指數搜尋（Exponential Search）
- 內插搜尋（Interpolation Search）

## 線性搜尋（Linear Search）

**線性搜尋（Linear Search）** 或稱 **循序搜尋法（Sequential Search）**，是一種最基本、最簡單、最低效的搜尋演算法。

**線性搜尋** 是透過迭代集合中的每個元素，一筆一筆資料比較，直到找到所要尋找的特定值為止或搜尋完整個範圍仍找不到為止。簡單來說就是從頭到尾找一遍，最大的特性為不需要事先排序集合。

舉例來說，迭代陣列尋找特定值：
```javascript
function linearSearch(arr, target) {
  const n = arr.length;
  for (let index = 0; index < n; index++) {
    if (target === arr[index]) {
      return index;
    }
  }
  return -1;
}
```
回傳第一個符合條件的元素索引位置，若沒有回傳 -1。

在 JavaScript 要在陣列中尋找特定元素，有以下方法使用：
- [`indexof()`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/indexOf) 類似上面的函式，但執行速度比自己寫的快。
- [`findIndex()`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex) 迭代陣列，回傳第一個滿足指定條件的元素索引，與 `indexof()` 的差異在於，`indexof()` 使用 `===` 比較，`findIndex()` 能使用 `callback` 函式定義條件，適用於複雜的元素（例，物件），而且就算找到符合的條件元素，也會迭代完整個陣列。
- 其他陣列搜尋的方法還有 [`some()`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/some) 、[`every()`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/every)、[`find()`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/find) 等等。

### 1. 分析

線性搜尋因為是從頭開始迭代至結尾，因此如果要找的資料在最後面或目標不存在，比較次數就會根據資料大小拉長。

- 特性：不需要事先排序
- 時間複雜度
  - 最佳：$O(1)$
  - 最差：$O(n)$
  - 平均：$O(n)$
- 空間複雜度為： $O(1)$


## 二分搜尋（Binary Search）

**二分搜尋（Linear Search）** 或稱 **折半搜尋演（Half-Interval Search）**、**對數搜尋（Logarithmic Search）**，是一種在 **「已排序陣列」** 中搜尋某一特定元素的搜尋演算法。


二分搜尋會將陣列中間位置的值與目標進行比較，再判斷目標在左還是右。每次判斷都會縮小一半的搜尋範圍，直到找到目標或目標不存在。

簡單來就說，就類似「終極密碼遊戲」，猜一個數字，會有三種回應：
1. 比目標小
2. 比目標大
3. 猜中了


### 1. 演算法實作

二分搜尋輸入的陣列必須是已排序狀態。

```javascript
function binarySearch(sortedArray, target) {
  let [start, end] = [0, sortedArray.length - 1];
  while (start <= end) {
    const mid = Math.floor((start + end) / 2);
    if (target === sortedArray[mid]) { return mid; }
    if (target < sortedArray[mid]) {
      end = mid - 1;
    } else {
      start = mid + 1;
    }
  }
  return -1;
}
```

雖然不建議，但可以使用遞迴結構來改寫：
```javascript
function binarySearch10(sortedArray, target) {
  return _binarySearch(0, sortedArray.length - 1);

  function _binarySearch(start, end) {
    if (start > end) { return -1; }
    const mid = Math.floor((start + end) / 2);
    if (target === sortedArray[mid]) { return mid; }
    if (target < sortedArray[mid]) {
      return _binarySearch(start, mid - 1);
    } else {
      return _binarySearch(mid + 1, end);
    }
  }
}
```
不過空間複雜度會從 $O(1)$ 變成 $O(\log n)$。

### 2. 分析

每次判斷都會縮小一半的搜尋範圍，因此整體執行時間約為 $\log_2$。

- 特性：需要事先排序陣列
- 時間複雜度
  - 最佳：$O(1)$
  - 最差：$O(\log n)$
  - 平均：$O(\log n)$
- 空間複雜度為： $O(1)$

二分搜尋與線性搜尋相比，速度是線性搜尋的指數倍，但使用二分搜尋的前提下，陣列必須先排序過。


## 指數搜尋（Exponential Search）

**指數搜尋（Exponential Search）** 或稱 **Galloping Search**，是一種特殊的二元搜尋，主要用在搜尋無限、無邊界的已排序序列。

指數搜尋不是從中間位置來判斷，而會從索引位置 $2^{0}$ 開始，不斷遞增 $2^{1}$、$2^{2}$ 直到 $2^{i}$ 位置，若比目標大，會停止指數成長，改用二用搜尋，也就是取中位置來回頭尋找目標。


### 1. 演算法實作

首先，我們先改寫二元搜尋，多傳入起始和終點的參數，也就是搜尋範圍：
```javascript
function binarySearch10(sortedArray, target, _start = 0, _end = sortedArray.length - 1) {
  let [start, end] = [_start, _end];
  while (start <= end) {
    const mid = Math.floor((start + end) / 2);
    if (target === sortedArray[mid]) { return mid; }
    if (target < sortedArray[mid]) {
      end = mid - 1;
    } else {
      start = mid + 1;
    }
  }
  return -1;
}
```

接下來是指數搜尋本體：
```javascript
function exponentialSearch(sortedArray, target) {
  const n = sortedArray.length;
  if (target === sortedArray[0]) { return 0; }
  let i = 1;
  while (i < n && sortedArray[i] < target) {
    i = i * 2;
  }
  if(sortedArray[i] === target) { return i; }
  return binarySearch(sortedArray, target, i / 2, Math.min(i, n - 1));
}
```


### 2. 分析

使用指數搜尋，若目標很靠近序列前端，那麼會提升執行效率。因為縮小搜尋範圍，效率會比二分搜尋高。

- 特性：需要事先排序陣列
- 時間複雜度
  - 最佳：$O(1)$
  - 最差：$O(\log n)$
  - 平均：$O(\log n)$
- 空間複雜度為： $O(1)$

## 內插搜尋（Interpolation Search）

**內插搜尋（Interpolation Search）** 又稱 **插補搜尋**、**插值搜尋**，也是一種特殊的二元搜尋，它是依照資料位置的分佈，利用內插公式預測目標的所在位置，搜尋方式與二分搜尋相同。

二分搜尋是預測中間位置，而內插搜尋則是用直線斜率預測目標位置，一般而言，資料量愈大，數值分佈會愈平均，內插搜尋的效率會比二分搜尋法好。

### 1. 內插公式

資料的值為 $y$、索引值為 $x$：
- 斜率 = $\dfrac{y_2 - y_1}{x_2 - x_1}$

目標值為 $k$、預測目標索引位置為 $m$：
- $\dfrac{y_2 - y_1}{x_2 - x_1}$ = $\dfrac{k - y_1}{m - x_1}$
- $m$ = $\dfrac{(k - y_1)(x_2 - x_1)}{y_2 - y_1} + x_1$

### 2. 演算法實作

```javascript
function interpolationSearch(sortedArray, target) {
  let [start, end] = [0, sortedArray.length - 1];
  while (start <= end) {
    const [x1, x2, y1, y2, k] = [start, end, sortedArray[start], sortedArray[end], target];
    const m = Math.floor(((k - y1) * (x2 - x1)) / (y2 - y1) + x1);
    if (m > end || m < start) { break; }
    if (target === sortedArray[m]) { return m; }
    if (target > sortedArray[m]) {
      start = m + 1;
    } else {
      end = m - 1;
    }
  }
  return -1;
}
```

### 3. 分析

如果資料分佈不均，近似線差異太大，最差時間複雜度高達 $O(n)$，如果資料分佈平均，執行效率會優於二元搜尋。

- 特性：需要事先排序陣列
- 時間複雜度
  - 最佳：$O(1)$
  - 最差：$O(n)$
  - 平均：$O(\log(\log n))$
- 空間複雜度為： $O(1)$

