---
title: "JavaScript 學演算法（二十二）- 遞迴 Recursion"
date: 2020-08-24
description: "為了理解遞迴，則必須首先理解遞迴。"
keywords: ["演算法 Algorithm", "遞迴", "Recursion", "竹白"]
tags: ["資料結構", "遞迴", "Recursion", "w3HexSchool"]
series: ["JavaScript 學演算法"]
categories: ["w3HexSchool六角鼠年鐵人賽"]
image: images/covers/2008/alg_recursion.png
draft: false
libraries:
- katex
---

這週是六角鼠年鐵人賽第三十週。

這幾週會說明演算法的設計：分治法、動態規劃、貪婪法、回溯法、分支定界法，所以我們先來看 **遞迴 Recursion**。

<!--more-->

{{< featuredImage >}}

## 什麼是遞迴

> 為了理解遞迴，則必須首先理解遞迴。

**遞迴（Recursion）** 最早是用在數學定義式，稱作 **遞迴定義（recursive definitions）**，使用被定義對象的自身來為其下定義，使用被定義對象的自身來為其下定義。

簡單來說，就是「自己定義自己」。

舉例來說，**費氏數列（Fibonacci）** 或稱 **斐波那契數列**：由 0 和 1 開始，之後的數是由之前的兩數相加而得出。
```
0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89 ...
```

如果要將數列全部列舉完，基本上不可能，所以數學上就以遞迴來定義：
- $F_0 = 0$（基本條件）
- $F_1 = 1$（基本條件）
- 對所有大於 $1$ 的整數 $n$：$F_n = F_{n-1} + F_{n-2}$（遞迴定義）


在程式語言中，將上述概念程式化，是程式流程控制的一種，**遞迴程序（recursive procedures）**，一個函式直接或間接呼叫自身的一種方式。

簡單來說，就是「自己呼叫自己」。

**遞迴**本質上，是一種將一個複雜的問題，拆分成具有相同性質的子問題，而解決問題的方法。

## 簡單範例

一個基本的遞迴函式一定要有：
1. 終止條件（基本條件）
2. 遞迴條件（呼叫自己的條件）

如果沒有終止條件，就會無限循環直到當掉。

### 1. 累加

假設輸入一個正整數 n，求 1 + 2 + ... + n 的總和。

最簡單的方式，就是用迭代的方式（迴圈），將所有數字累加再一起：
```javascript
function sum(n) {
  let result = 0;
  for (let i = 1; i <= n; i++) {
    result += i;
  }
  return result;
}
```

這個問題可以寫成 $S(n) = n + n - 1 + ... + 1$，也就是 
- $S(1) = 1$（基本條件）
- 對所有大於 1 的整數：$S(n) = n + S(n - 1)$（遞迴定義）

使用遞迴的方式：
```javascript
function sum(n) {
  if (n === 1) {
    return 1;
  }
  return n + sum(n - 1);
}
```
初始（終止）條件為 `n === 1`，遞迴條件為 `n !== 1`。

如果 `n = 6`，執行過程如下：
```javascript
sum(5)
  5 + sum(4)
    4 + sum(3)
      3 + sum(2)
        2 + sum(1)
          return 1
        return 2 + 1   // 3
      return 3 + 3     // 6
    return 4 + 6       // 10
  return 5 + 10        // 15
```

### 2. 階乘

另一個更常見的範例就是正整數的**階乘**（factorial）。

定義：
- $0! = 1$
- $n! = 1 \times 2 \times 3 ...(n - 2) \times (n - 1) \times n$
- $n! = n \times(n - 1)!$

```javascript
function factorial(n) {
  if (n === 0) {return 1;}
  return n * factorial(n - 1);
}
```

### 3. 費氏數列

上面提到的費氏數列：
- $F_0 = 0$（基本條件）
- $F_1 = 1$（基本條件）
- 對所有大於 $1$ 的整數 $n$：$F_n = F_{n-1} + F_{n-2}$（遞迴定義）

```javascript
function fibonacci(n) {
  if (n < 2) {
    return n;
  }
  return fibonacci(n - 1) + fibonacci(n - 2);
}
```

## 遞迴與迭代

迭代：
- 迴圈結構，是由下而上（Bottom-Up），一步步逼近答案；
- 用新值覆蓋舊值，直到滿足條件後結束，因為不保存中間值，因此不會消耗很多記憶體空間。

遞迴：
- 選擇結構，是由上而下（Top-Down），慢慢地將問題縮小，來求得答案；
- 將問題分解成干個子問題，再回頭運算答案，因此會消耗大量記憶體空間，但程式碼簡單明瞭。

遞迴可以改寫成迭代，迭代反之亦然。

### 1. 遞迴的優缺點

優點：
- 大問題化為小問題，程式碼簡潔清晰，可讀性佳。

缺點：
- 容易產生 [堆疊溢位（stack overflow）](https://zh.wikipedia.org/wiki/%E5%A0%86%E7%96%8A%E6%BA%A2%E4%BD%8D)，因此非必要，不建議使用遞迴；
- 冗餘計算，若子問題彼此非獨立的，會重複計算。

也就是說，雖然遞迴看起來比較簡潔專業，但並沒有比較快且消耗大量記憶體。

## 尾遞迴

**呼叫堆疊**是有大小的，當過多的函式呼叫導致無法容納這些呼叫的位址，就會發生**堆疊溢位**。常發生在使用遞迴函式，因此設計函式時，會盡量避免使用遞迴結構，會改用迭代結構。

但一定要使用遞迴呢？有一個技巧叫做**尾遞迴（tail-recursive）**，可以優化遞迴函式指的是遞迴操作只回傳函式自己。

不過我沒研究過，因此這裡先不討論，有興趣可以參考以下連結：
- [尾呼叫 - wikipedia](https://zh.wikipedia.org/wiki/%E5%B0%BE%E8%B0%83%E7%94%A8)
- [尾遞歸為啥能優化？ - 知乎](https://zhuanlan.zhihu.com/p/36587160)
- [Javascript中的尾遞歸及其優化 - 知乎](https://zhuanlan.zhihu.com/p/47155064)
- [讓遞迴的Stack永遠不會爆炸的「尾遞迴」真的有那麼神奇嗎 — 尾遞迴篇｜髒桶子](https://ithelp.ithome.com.tw/articles/10231115)

