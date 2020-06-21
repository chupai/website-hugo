---
title: "JavaScript 學演算法（十）- 快速排序"
date: 2020-06-01
description: "快速排序（Quick Sort） 是實用性很高的排序法，與合併排序一樣是使用 分治法的應用。"
keywords: ["演算法 Algorithm", "排序演算法", "快速排序","竹白"]
tags: ["排序演算法", "快速排序", "w3HexSchool"]
series: ["JavaScript 學演算法"]
categories: ["w3HexSchool六角鼠年鐵人賽"]
image: images/covers/2000601_sort_algorithm_quick_sort.png
draft: false
libraries:
- katex
---

這週是六角鼠年鐵人賽第十八週，這週簡單說明 **快速排序（Quick Sort）**。

<!--more-->

{{< featuredImage >}}

## 快速排序（Quick Sort）

**快速排序（Quick Sort）** 是實用性很高的排序法，與合併排序一樣是使用 [**分治法**](https://zh.wikipedia.org/wiki/%E5%88%86%E6%B2%BB%E6%B3%95) 的應用。

操作流程：
1. 快速排序是從數列中隨機選擇一個數作為 **基準（pivot）**。
2. 將比基準小的移至 **基準** 左邊，形成左區塊
3. 將比基準大的移至 **基準** 右邊，形成右區塊
4. 分別對左邊和右邊的區塊重複前項 3 操作（遞迴），直到區塊中只剩一個元素。

## 演算法實作

### 1. 簡單好懂版本

此版本使用遞迴結構，並使用額外空間儲存比較後的元素，不會改變原陣列。

```javascript
function quickSort(arr) {
  const n = arr.length;
  if (n < 2) { return arr; } // 1
  
  const pivot = arr[0];  // 2
  const [leftArr, rightArr] = [[], []];

   // 3
  for (let i = 1; i < n; i++) {
    if (pivot > arr[i]) {
      leftArr.push(arr[i]);
    } else {
      rightArr.push(arr[i]);
    }
  }
  // 4
  return [...quickSort(leftArr), pivot, ...quickSort(rightArr)];
}
```
1. 遞迴停止條件：如果陣列內只剩一個元素就停止或陣列為空，也可以寫成 `n <= 1`。
2. 選擇第一個元素作為基準。
3. 比較元素，將元素分為左右兩邊。
4. 遞迴、合併（小的放基準前面，大的放後面），也可以寫成 `quickSort(left).concat([pivot], quickSort(rightArr))`。


此版本雖然簡單好懂，但空間複雜度不佳，會消耗大量記憶體。

### 2. 原地（in-place）劃分版本

原地（in-place）劃分版本是使用交換位置來劃分兩個區塊。

將第一個元素作為 **基準**，比較元素後，會分成兩個區塊：
- 左邊區塊：比基準小的元素
- 右邊區塊：比基準大的元素，包含等於

流程：
1. 右邊區塊第一個元素預設為第二個位置。
2. 當遇到比基準小的值，會與右邊區塊的第一個值交換位置。
3. 全部比較完畢後，基準與左邊區塊最後一個元素交換位置。

<iframe height="600" style="width: 100%;" scrolling="no" title="快速排序 - 劃分操作：第一個元素為基準" src="https://codepen.io/CHUPAIWANG/embed/gOadmMG?height=299&theme-id=dark&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/gOadmMG'>快速排序 - 劃分操作：第一個元素為基準</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

劃分操作：
```javascript
function partition(arr, start, end) {
  const swap = (arr, a, b) => [arr[a], arr[b]] = [arr[b], arr[a]];  // 1
  
  const pivot = arr[start];      // 2
  let firstRightIndex = start + 1; // 3

  // 4
  for (let i = start + 1; i <= end; i++) {
    // 5
    if (arr[i] < pivot) {
      swap(arr, i, firstRightIndex);
      firstRightIndex += 1;
    }
  }
  const lastLeftIndex = firstRightIndex - 1;  // 6
  swap(arr, start, lastLeftIndex);
  
  return lastLeftIndex;
}
```

1. 陣列交換位置函式
2. 將第一元素作為基準元素。
3. 紀錄目前左邊區塊的第一元素位置，預設為基準元素的下一個。
4. 從第二個元素開始遍歷至最後一個。
5. 遇到比基準小的元素就交換，並遞增紀錄變數 `firstRightIndex`。
6. `firstMaxIndex - 1` 右邊區塊的第一元素位置的前一個就是左邊區塊最後一個元素。
7. 最後回傳目前基準元素的位置，因為已經交換位置了，所以回傳 `lastLeftIndex`。

接下來回到函式本體：
```javascript
function quickSort(arr, start = 0, end = arr.length - 1) {
  if( start >= end) { return arr; }  // 1
  const pivotIndex = partition(arr, start, end);  // 2
  // 3
  quickSort(arr, start, pivotIndex - 1);
  quickSort(arr, pivotIndex + 1, end);
  
  return arr;
}
```
1. 停止歸遞條件
2. 執行劃分操作
3. 歸遞


最終程式碼：
```javascript
function quickSort(arr, start = 0, end = arr.length - 1) {
  if (start >= end) {
    return arr;
  }
  const pivotIndex = partition(arr, start, end);
  quickSort(arr, start, pivotIndex - 1);
  quickSort(arr, pivotIndex + 1, end);
  return arr;

  function partition(arr, start, end) {
    const swap = (arr, a, b) => ([arr[a], arr[b]] = [arr[b], arr[a]]);
    const pivot = arr[start];
    let firstRightIndex = start + 1;
    for (let i = start + 1; i <= end; i++) {
      if (arr[i] < pivot) {
        swap(arr, i, firstRightIndex);
        firstRightIndex += 1;
      }
    }
    const lastLeftIndex = firstRightIndex - 1;
    swap(arr, start, lastLeftIndex);
    return lastLeftIndex;
  }
}
```

### 3. 優化基準的選擇

快速排序時間複雜最佳的情況必須剛好 **基準** 為數列的中間值，如果選取到最小或最大值，則會是最糟的情況。那麼我們選擇第一個元素作為 **基準**，如果數列已是排序狀態，就會導致演算法時間複雜度是最糟結果。

為了避免這種極端的 **基準** 出現，比較常見的做法為選取中間位置或是隨機選取作為 **基準**，選取到最小或最大值的機率會降低。

取中間位置的元素，再比較前需先將元素換到開頭。
<iframe height="650" style="width: 100%;" scrolling="no" title="快速排序 - 劃分操作：中間元素為基準" src="https://codepen.io/CHUPAIWANG/embed/jObebWV?height=265&theme-id=dark&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/jObebWV'>快速排序 - 劃分操作：中間元素為基準</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

```javascript
function partition(arr, start, end) {
  const swap = (arr, a, b) => [arr[a], arr[b]] = [arr[b], arr[a]];
  
  const mid = Math.floor((start + end) / 2);  // 1
  const pivot = arr[mid];
  let firstRightIndex = start + 1;
  
  swap(arr, mid, start);  // 2
  for (let i = start + 1; i <= end; i++) {
    if (arr[i] < pivot) {
      swap(arr, i, firstRightIndex);
      firstRightIndex += 1;
    }
  }
  const lastLeftIndex = firstRightIndex - 1;
  swap(arr, start, lastLeftIndex);
  
  return lastLeftIndex;
}
```
1. 取中間位置索引。
2. 將基準元素先換至開頭。

隨機選取基準：
```javascript
// 1 改成
const pivotIndex = Math.floor(Math.random() * (end - start + 1) + start);
const pivot = arr[pivotIndex];
// 2 改成
swap(arr, pivotIndex, start);
```

<iframe height="650" style="width: 100%;" scrolling="no" title="快速排序 - 劃分操作：隨機選取基準" src="https://codepen.io/CHUPAIWANG/embed/XWmxmaY?height=265&theme-id=dark&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/XWmxmaY'>快速排序 - 劃分操作：隨機選取基準</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

除了選取中間位置元素和隨機選取元素作為 **基準**，另外還有其他避免極端情況的優化方式：
1. 三數中位數 Median of three
2. 中位數的中位數 Median of medians


### 4. 迭代結構

跟合併排序一樣，當資料數量非常非常大時，歸遞結構的操作很容易導致 **[堆疊溢位](https://zh.wikipedia.org/wiki/%E5%A0%86%E7%96%8A%E6%BA%A2%E4%BD%8D)**，因此可以改寫成迭代結構。

我們可以用陣列模擬堆疊空間，將待劃分區塊的 `start` 和 `end` 保存在裡面。
```javascript
function quickSort1(arr) {
  const stack = [];
  stack.push(0);
  stack.push(arr.length - 1);

  while (stack.length > 0) {
    const end = stack.pop();
    const start = stack.pop();

    if (start < end) {
      const pivotIndex = partition(arr, start, end);
      stack.push(start);
      stack.push(pivotIndex - 1);
      stack.push(pivotIndex + 1);
      stack.push(end);
    }
  }
  return arr;
}
```

## 分析

簡易分析，若每次都會將數列剛好劃分成一半，反覆劃分直到剩一個元素，需要操作 $\log_{2}n$ 次。每次劃分操作每個元素都會執行比較，也就是 $n$ 次，因此整體執行時間約為 $n \log_{2} n$。

但 **基準** 如果選取到最小或最大值，每次劃分元素都會集中一邊，需要操作 $n$ 次 才會剩一個元素。因此整體執行時間會變成為 $n^2$。

- 不穩定排序
- 時間複雜度
  - 最佳：$O(n \log n)$
  - 最差：$O(n^2)$
  - 平均：$O(n \log n)$
- 空間複雜度為：根據實現的方式不同而不同

