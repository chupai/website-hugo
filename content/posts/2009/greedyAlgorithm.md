---
title: "JavaScript 學演算法（二十四）- 貪婪演算法"
date: 2020-09-20
description: "貪婪演算法。"
keywords: ["演算法 Algorithm", "貪婪演算法", "動態規劃", "竹白"]
tags: ["資料結構", "貪婪演算法", "動態規劃", "w3HexSchool"]
series: ["JavaScript 學演算法"]
categories: ["w3HexSchool六角鼠年鐵人賽"]
image: images/covers/2009/greedyAlgorithm.png
draft: false
libraries:
- katex
---

這週是六角鼠年鐵人賽第三十三週。

<!--more-->

{{< featuredImage >}}

## 何謂貪婪演算法

[**貪婪演算法（Greedy Algorithm）**](https://zh.wikipedia.org/wiki/%E8%B4%AA%E5%BF%83%E7%AE%97%E6%B3%95)，或稱 **貪心演算法**，比起動態規劃更為簡單直接。

### 1. 演算法思想

貪婪演算法的主軸是**反覆執行某個任務**，直到求得一組解；每次執行任務時，都是找當下最好的方案。

也就是說，貪婪演算法並不會考慮到未來，只考慮當下，因此對於某些情況下不一定能求出最佳解。

### 2. 適用的情況

由於貪婪演算法的高效性以及其所求得的答案比較接近最佳解，貪婪演算法也可以用作輔助演算法或者直接解決一些要求結果不特別精確的問題。

## 零錢問題

給定不同面額的硬幣 `coins`，和一個總金額 `amount`，求出最少硬幣的組合數。如果沒有任何一種硬幣組合能組成總金額，回傳 `-1`。

### 1. 貪婪演算法

假設我們的硬幣面額為新台幣常見的 1、5、10、50。

那麼我們可以使用貪婪演算法的思想，每次兌換優先使用面額較大的硬幣：

```javascript
function coinChange(coins, amount) {
  let count = 0;
  let total = 0;
  for (let i = coins.length; i >= 0; i -= 1) {
    const coin = coins[i];
    while (total + coin <= amount) {
      count += 1;
      total += coin;
    }
  }
  return count;
}

const COINS = [1, 5, 10, 50];

console.log(coinChange(COINS, 90)); // 5
console.log(coinChange(COINS, 40)); // 4
console.log(coinChange(COINS, 15)); // 2
```
對於新台幣硬幣面額，我們能使用貪婪演算法求出最佳解。

但是，如果使用其他硬幣面額，就不一定能求出最佳解：
```javascript
const COINS = [1, 3, 4];

console.log(coinChange(COINS, 6)); // 3
```
正確解為 `2(3 + 3)`。

### 2. 動態規劃

改用動態規劃。

假設硬幣面額為 `[1, 3, 4]`、總金額為 `100`。

`dp[i]`：表示總金額為 `i` 的時候最佳解。
- 如果我們拿一枚面額為 1 的硬幣 + 總金額 `99` 的解為 `dp[99]` + `1`；
- 如果我們拿一枚面額為 3 的硬幣 + 總金額 `97` 的解為 `dp[97]` + `1`；
- 如果我們拿一枚面額為 4 的硬幣 + 總金額 `96` 的解為 `dp[96]` + `1`。

由此可知，總金額 `100` 的最佳解為 3 種解法的其中一種，因此我們可以得到：`dp[100] = Math.min(dp[99] + 1, dp[97] + 1, dp[96] + 1)`。

推導出：`dp[i] = Math.min(dp[i - coins[0]] + 1, dp[i - coins[1]] + 1, ...)`

```javascript
function coinChange(coins, amount) {
  let dp = new Array(amount + 1).fill(Infinity);
  dp[0] = 0;
  for (let coin of coins) {
    for (let i = coin; i <= amount; i += 1) {
      dp[i] = Math.min(dp[i], dp[i - coin] + 1);
    }
  }
  return dp[amount] === Infinity ? -1 : dp[amount];
}

const COINS = [1, 3, 4];

console.log(coinChange(COINS, 6)); // 2
```
先建立 `amount + 1` 陣列（`+1` 是為了對應總金額），將預設值設為 `Infinity`（便於與最小值的判斷）。

接下來遍歷每種面額對應的金額，並判斷是否為最小解。