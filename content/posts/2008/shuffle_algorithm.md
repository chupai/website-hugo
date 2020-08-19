---
title: "JavaScript 學演算法（二十一）- 洗牌演算法"
date: 2020-08-17
description: "洗牌（shuffle） 其實就是亂數排序。"
keywords: ["演算法 Algorithm", "洗牌演算法", "竹白"]
tags: ["洗牌演算法", "w3HexSchool"]
series: ["JavaScript 學演算法"]
categories: ["w3HexSchool六角鼠年鐵人賽"]
image: images/covers/2008017_shuffle_algorithm.png
draft: false
libraries:
- katex
---

這週是六角鼠年鐵人賽第二十九週。

<!--more-->

{{< featuredImage >}}

## 什麼是洗牌

**洗牌（shuffle）** 其實就是亂數排序，將一組陣列中的元素順序打亂，是一種很常用到的演算法，在生活中最常見的例子就是洗撲克牌。

洗牌演算法的條件：
1. 結果要能夠覆蓋所有的情況；
2. 所有出現的結果機率相等。

假設有一個長度為 $n$ 的陣列，會有 $n!$ 種不同的組合情況。

演算法必須可以產生出 $n!$ 中的每一種結果，不可以多或少，否則每種結果出現的機率不相等。

## Fisher-Yates shuffle

Fisher-Yates shuffle 是最標準的洗牌演算法。

其實它就是隨機交換：
```javascript
function shuffle(arr) {
  const n = arr.length;
  for (let i = n - 1; i > 0; i -= 1) {
    const rand = Math.floor(Math.random() * (i + 1));
    [arr[i], arr[rand]] = [arr[rand], arr[i]];
  }
}
```
從後面迭代陣列，產生隨機索引與未排序的最後一個元素交換位置（隨機索引包含自己）。

讓我們來分析一下，假設陣列元素有 5 個：
1. 第 1 次迭代：`i = 4`，`rand` 的範圍是 `0 ~ 4`
2. 第 2 次迭代：`i = 3`，`rand` 的範圍是 `0 ~ 3`
3. 第 3 次迭代：`i = 2`，`rand` 的範圍是 `0 ~ 2`
4. 第 4 次迭代：`i = 1`，`rand` 的範圍是 `0 ~ 1`
5. 第 5 次迭代：`i = 0`，`rand` 的範圍是 `0`

整個過程產生的所有可能結果為：
- $n! = 5! = 5 \times 4 \times 3 \times 2 \times 1$
- 時間複雜度為 $O(n)$

`i >= 0` 這樣寫也可以，因為最後一次迭代可有可無：
```javascript
function shuffle(arr) {
  const n = arr.length;
  for (let i = n - 1; i >= 0; i -= 1) {
    const rand = Math.floor(Math.random() * (i + 1));
    [arr[i], arr[rand]] = [arr[rand], arr[i]];
  }
}
```

或者將交換位置改成前面：
```javascript
function shuffle(arr) {
  const n = arr.length;
  for (let i = 0; i < n; i += 1) {
    const rand = Math.floor(Math.random() * (n - i)) + i;
    [arr[i], arr[rand]] = [arr[rand], arr[i]];
  }
}
```
假設陣列元素有 5 個：
1. 第 1 次迭代：`i = 0`，`rand` 的範圍是 `0 ~ 4`
2. 第 2 次迭代：`i = 1`，`rand` 的範圍是 `1 ~ 4`
3. 第 3 次迭代：`i = 3`，`rand` 的範圍是 `2 ~ 4`
4. 第 4 次迭代：`i = 4`，`rand` 的範圍是 `3 ~ 4`
5. 第 5 次迭代：`i = 5`，`rand` 的範圍是 `4`

最終結果為 $5! = n!$，一樣符合洗牌演算法條件。

## 錯誤的演算法

網路上有很多洗牌演算法實作其實是錯誤的，因為它看似隨機打亂順序，但分布率並不均勻，以下舉幾個常見的錯誤。

第一種常見的錯誤，順序隨機洗牌：
```javascript
function shuffle(arr) {
  const n = arr.length;
  for (let i = 0; i < n; i += 1) {
    const rand = Math.floor(Math.random() * n);
    [arr[i], arr[rand]] = [arr[rand], arr[i]];
  }
}
```
隨機範圍不變，因此每次迭代都會有 $n$ 種結果，最終結果為 $n^n$。

假設陣列元素有 5 個，最終產生的結果可能有 $5^5 = 3125$ 種，但最大的組合結果只有 $5! = 120$，這表示某些組合出現的機率會比較大，因此不符合條件。


另一種常見的錯誤，使用 `sort()`：
```javascript
array.sort(() => Math.random() - 0.5);
```
利用 -0.5 ~ 0.5 的數字，讓 `sort()` 的比較器函式 `a` 和 `b` 之間的大小關係是隨機的，所以排序結果也是隨機的。

這種作法雖然簡潔優雅，但是它有很大的問題：
- 雖然每次比較的結果是隨機且均勻的，但各家瀏覽器實作 `sort()` 的方式不同，因此無法保證比較的次數。
- 假設比較次數為 $m$，每次每次比較的發生機率為 $1 / 2$，因此最終產生的結果為 $2^m$。