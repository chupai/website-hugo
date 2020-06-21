---
title: "JavaScript 學演算法（十一）- 希爾排序"
date: 2020-06-08
description: "希爾排序（Shell Sort） 是插入排序的一種更高效的改進版本。"
keywords: ["演算法 Algorithm", "排序演算法", "希爾排序","竹白"]
tags: ["排序演算法", "希爾排序", "w3HexSchool"]
series: ["JavaScript 學演算法"]
categories: ["w3HexSchool六角鼠年鐵人賽"]
image: images/covers/200608_sort_algorithm_shell_sort.png
draft: false
libraries:
- katex
---

這週是六角鼠年鐵人賽第十九週，這週來說明 **希爾排序（Shell Sort）**。

<!--more-->

{{< featuredImage >}}

## 希爾排序（Shell Sort）

**希爾排序（Shell Sort）**，也稱 **遞減增量排序**，是 **插入排序** 的一種更高效的改進版本，為不穩定排序。

**插入排序（Insertion Sort）** 的兩點特性：
1. 在已經排好序的資料操作時，是效率高的，即可以達到 $O(n)$。
2. 但一般情況是低效，每次只能將資料移動一位。

而 **希爾排序** 是在 **插入排序** 基礎上添加了 **間隔長度（gap）** 的概念，使得 **插入排序** 可以分組執行，並且資料的移動距離可以大於一。

其概念為，將整個陣依照預先指定的 **gap**，交錯分割成數個小陣列，並以插入排序的方式將這些小陣列個別排序，然後逐漸縮小 **gap**，直到 **gap** 等於 1。此時再作最後一次插入排序。

舉例：
```
nums = [9, 8, 7, 6, 5, 4, 3, 2, 1]

gap = 3
9 8 7 6 5 4 3 2 1
9     6     3     => 3 6 9
  8     5     2   => 2 5 8
    7     4     1 => 1 4 7
3 2 1 6 5 4 9 8 7

gap = 2
3 2 1 6 5 4 9 8 7
3   1   5   9   7 => 1 3 5 7 9
  2   6   4   8   => 2 4 6 8
1 2 3 4 5 6 7 8 9

gap = 1
1 2 3 4 5 6 7 8 9 => 1 2 3 4 5 6 7 8 9
```

## 演算法實作

這是基本的插入排序：
```javascript
function insertionSort(arr) {
  const n = arr.length;
  for (let i = 1; i < n; i++) {
    const temp = arr[i];
    let j = i;
    while (j > 0 && temp < arr[j - 1]) {
      arr[j] = arr[j - 1];
      j -= 1;
    }
    arr[j] = temp;
  }
  return arr;
}
```

希爾排序的設計者 Donald Shell 最初建議選擇 `gap = n / 2` 比較好理解。

```javascript
function shellSort(arr) {
  const n = arr.length;
  let gap = Math.floor(n / 2);
  
  while (gap > 0) {
    // 與插入排序極為相似
    for (let i = gap; i < n; i++) {
      const temp = arr[i];
      let j = i;
      while (j >= gap && temp < arr[j - gap]) {
        arr[j] = arr[j - gap];
        j -= gap;
      }
      arr[j] = temp;
    }
    gap = Math.floor(gap / 2); // 縮小間隔長度
  }
  return arr;
}
```

簡易執行時間比較：
<iframe height="450" style="width: 100%;" scrolling="no" title="Shell Sort" src="https://codepen.io/chupai/embed/GRpwKzP?height=265&theme-id=dark&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/chupai/pen/GRpwKzP'>Shell Sort</a> by Chupai@Design
  (<a href='https://codepen.io/chupai'>@chupai</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## 優化間隔長度

間隔長度的選擇是是希爾排序的重要部分。

希爾排序的時間複雜度不容易計算，因為會根據間距值來決定。

- 使用最差的間距，最差時間複雜度：$O(n^2)$
- 使用最佳的間距，最佳時間複雜度：$O(n \log n)$
- 平均複雜會根據間距值來決定
- 空間複雜度為： $O(1)$

最佳的間隔長度是透過複雜的數學公式所計算出來的，而且會根據資料大小，而有所不同。這裡不做深究，相關細節可參考 **[wikipedia](https://en.wikipedia.org/wiki/Shellsort#Gap_sequences)**。

我們以最常見的 Marcin Ciura 為例。

定義間隔長度常數，並在執行排序時傳入：
```javascript
const CIURA_GAPS = [1750, 701, 301, 132, 57, 23, 10, 4, 1];

function shellSort(arr, gaps) {
  const n = arr.length;

  for (let gap of gaps) {
    for (let i = gap; i < n; i++) {
      const temp = arr[i];
      let j = i;
      while (j >= gap && temp < arr[j - gap]) {
        arr[j] = arr[j - gap];
        j -= gap;
      }
      arr[j] = temp;
    }
  }

  return arr;
}

shellSort(arr, CIURA_GAPS);
```

