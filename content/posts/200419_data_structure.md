---
title: "JavaScript 學演算法（三）- 資料結構概述"
date: 2020-04-19
description: "這週是六角鼠年鐵人賽第十一週，學習基礎演算法之前，先來瞭解基本的資料結構。"
keywords: ["演算法 Algorithm", "資料結構", "JavaScript 學演算法","竹白"]
tags: ["演算法 Algorithm", "資料結構", "w3HexSchool"]
series: ["JavaScript 學演算法"]
categories: ["w3HexSchool六角鼠年鐵人賽"]
image: images/covers/200419_data_structure.png
draft: false
---

這週是 六角鼠年鐵人賽 第十一週，學習基礎演算法之前，先來瞭解基本的資料結構。

<!--more-->
{{< featuredImage >}}

## 為什麼要學習資料結構

因為不同的資料結構，將會影響程式設計（演算法）的方法與效率。

>程式 = 資料結構 + 演算法
Algorithms + Data Structures = Programs

選擇正確的資料結構，可以提高演算法的效率。

## 什麼是資料結構

在電腦科學中，資料結構（Data Structure）是電腦中儲存、組織資料的方式。簡單來說，當資料存在記憶體中的時候，決定資料的存放順序及存放位置的，就是資料結構。

舉例來說，假設要儲存考試成績，你不會用變數一個一個去存它，通常會用一個陣列來存放：
```javascript
let score1 = 80;
let score2 = 60;
let score3 = 70;

let scores = [80, 60 , 70];
```
因為當要儲存的資料變多時，用變數一個一個去存資料，要處理資料時，非常麻煩。

換句話說，所謂資料結構，就是考慮如何運用變數儲存要處理的資料，而演算法就是要處理這些資料的方法。將資料有組織的存放在程式內，可以提升程式的執行效率。

## 常見的資料結構

- 陣列（Array）
- 連結串列（Linked List）
- 堆疊（Stack）
- 佇列（Queue） 
- 樹狀結構（Tree）
- 圖形結構（Graph）
- 堆積（Heap）
- 雜湊表（Hash table）

## 資料結構的分類

資料結構，根據元素之間的關係，分為兩大類：
- 線性關係的資料結構
- 非線關係的性結構

### 1. 線性關係

線性關係的資料結構，其組成資料元素間具有線性關係，簡單來說就是「資料會排成一直線」。

每個元素只有另外兩個元素相鄰，也就是只有前、後兩個方向：
- 第一個元素沒有前驅者（前面的元素）
- 最後一個元素沒有後繼者（後面的元素）
- 其餘的元素都各只有一個前驅者（predecessor）和一個後繼者（successor）

線性關係的資料結構又分為兩種物理結構：
- 順序儲存結構，例：陣列、堆疊、佇列
- 鏈式儲存結構，例：連結串列

### 2. 非線性關係

而非線性關係的資料結構，簡單來說就是資料元素間不是只有前後關係。

非線性關係的資料結構包含：
- 階層關係，例：樹
- 相鄰關係，例：圖

## 資料結構操作

常見的資料結構操作的時間複雜度與空間複雜度。

![](https://i.imgur.com/1aItTLI.png)
*[圖片來源：Know Thy Complexities!](https://www.bigocheatsheet.com/)*


## 總結

不過大多數情況下 Javascript 開發關心的是「資料的邏輯結構」而非「資料的儲存結構」。因為 JavaScript 只有物件，陣列實際上也是物件，多數的資料結構都必須使用物件或陣列來模擬實作。
