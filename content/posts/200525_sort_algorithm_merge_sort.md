---
title: "JavaScript 學演算法（九）- 合併排序"
date: 2020-05-25
description: "合併排序（Merge Sort） 又稱為 歸併排序，是建立在合併操作上的一種有效的排序演算法，是採用 分治法 的一個非常典型的應用。"
keywords: ["演算法 Algorithm", "排序演算法", "合併排序","竹白"]
tags: ["排序演算法","排序演算法", "合併排序", "w3HexSchool"]
series: ["JavaScript 學演算法"]
categories: ["w3HexSchool六角鼠年鐵人賽"]
image: images/covers/200525_sort_algorithm_merge_sort.png
draft: false
libraries:
- katex
---

這週是六角鼠年鐵人賽第十七週，這週簡單說明 **合併排序（Merge Sort）**。

<!--more-->

{{< featuredImage >}}

## 合併排序（Merge Sort）

**合併排序（Merge Sort）** 又稱為 **歸併排序**，是建立在合併操作上的一種有效的排序演算法，是採用 [**分治法**](https://zh.wikipedia.org/wiki/%E5%88%86%E6%B2%BB%E6%B3%95) 的一個非常典型的應用。

分治法（Divide and Conquer）簡單來說就是將問題分成兩個子問題或更多的子問題，再繼續將子問題分解成更小的子問題，直到可以簡單求解，接著將所有子問題的結果合併，就能求得元問題的解。分治法一般會使用遞迴實現。

操作流程，採用分治法，分為兩個操作：
1. 分割：將數列平均分割成兩半，重複操作直到區塊中只剩一個元素。
2. 合併：接著再將各個子陣列合併，合併時會反覆比較第一個數，並將較小的元素移至上一層中。重複操作直到剩一個數列，排序完畢。


合併排序的實現有兩種方法：
- 遞迴結構（自頂向下 Top-down）
- 迭代結構（自底向上 Bottom-up）

一般來說，合併排序是使用遞迴結構來實現的，但遞迴有一個問題，就是需要使用到額外空間來儲存子陣列，而且當遞迴函式被呼叫的過於頻繁，可能導致 **[堆疊溢位](https://zh.wikipedia.org/wiki/%E5%A0%86%E7%96%8A%E6%BA%A2%E4%BD%8D)**。

為了優化合併排序（迭代結構），我們可以跳過分割這個操作，也就是一開始就將它們視為已分割狀態，比較相鄰兩個元素進行合併操作，每輪進行合併操作的元素都會翻倍成長。

## 遞迴結構實作

遞迴結構一共需要兩個函式，並透過遞迴函式的方式來處理。

### 1. 分割操作

```javascript
function mergeSort(arr) {
  const n = arr.length; // 1
   // 2
  if (n < 2) {
    return arr;
  }
  const midIndex = Math.floor(n / 2); // 3
  // 4
  const leftArray = arr.slice(0, midIndex);
  const rightArray = arr.slice(midIndex);

  // 5
  mergeSort(leftArray);
  mergeSort(rightArray);
}
```
1. 陣列元素數量。
2. 遞迴停止條件：如果陣列內只剩一個元素就停止或陣列為空。
3. 取陣列的中間索引。
4. 使用 `slice()` 將陣列分割成兩個子陣列。
5. 使用遞迴繼續分割兩個子陣列。


### 2. 合併操作

合併操作函式，會傳入兩個陣列，並依元素大小排序：
```javascript
function merge(leftArr, rightArr) {
  const temp = []; // 1
  // 2
  while (leftArr.length && rightArr.length) {
    // 3
    if (leftArr[0] < rightArr[0]) {
      temp.push(leftArr.shift());
    } else {
      temp.push(rightArr.shift());
    }
  }

  return temp.concat(leftArr, rightArr); // 4
}
```
1. 宣告變數儲存合併結果。
2. `while` 迴圈終止條件：當一個陣列為空就跳出迴圈。
3. 比較大小後，使用 `shift()` 將元素刪除，並加到結果陣列中。
4. 將剩餘元素的陣列與結果合併。

以上實作法方邏輯上沒問題，網路上很多文章的範例也都是這樣實作的（包含 [維基百科](https://zh.wikipedia.org/wiki/%E5%BD%92%E5%B9%B6%E6%8E%92%E5%BA%8F)） 。

但 `shift` 方法背後原理操作，在刪除第一個元素後，其餘的元素都會往前移動，其實並不高效。

因此建議使用以下方式來實作：
```javascript
function merge(leftArr, rightArr) {
  const temp = []; // 1
  let [leftIndex, rightIndex] = [0, 0]; // 2
  // 3
  while (leftIndex < leftArr.length && rightIndex < rightArr.length) {
    
    if (leftArr[leftIndex] < rightArr[rightIndex]) {
      // 4
      temp.push(leftArr[leftIndex]);
      leftIndex += 1;
    } else {
      temp.push(rightArr[rightIndex]);
      rightIndex += 1;
    }
  }
  // 5
  while (leftIndex < leftArr.length) {
    temp.push(leftArr[leftIndex]);
    leftIndex += 1;
  }
  while (rightIndex < rightArr.length) {
    temp.push(rightArr[rightIndex]);
    rightIndex += 1;
  }
  return temp;
}
```
1. 宣告變數儲存合併結果。
2. 宣告變數來紀錄當前索引。
3. `while` 迴圈終止條件：當前索引大於陣列長度，表示這個陣列都加到結果陣列中了。
4. 比較大小後，將元素放進結果陣列中，並將當前索引往前（+1）。 
6. 將剩餘元素的陣列與結果合併。

更簡潔的寫法：
```javascript
// 4
temp.push(leftArr[leftIndex++]);

// 5
return [...temp, ...leftArr.slice(leftIndex), ...rightArr.slice(rightIndex)];
```

以下為使用 `shift()` 和使用索引兩種方式的簡易的執行時間比較，你可以發現，雖然 `shift()` 實作簡潔好理解，但執行速度差異非常大。
<iframe height="500" style="width: 100%;" scrolling="no" title="合併排序（Merge Sort）" src="https://codepen.io/chupai/embed/GRpdNGj?height=265&theme-id=dark&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/chupai/pen/GRpdNGj'>合併排序（Merge Sort）</a> by Chupai@Design
  (<a href='https://codepen.io/chupai'>@chupai</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 3. 完成演算法

將分割的子陣列，呼叫 `merge()` 合併：
```javascript
function mergeSort(arr) {
  const n = arr.length;
  if (n < 2) {
    return arr;
  }
  const midIndex = Math.floor(n / 2);
  const leftArray = arr.slice(0, midIndex);
  const rightArray = arr.slice(midIndex);

  return merge(mergeSort(leftArray), mergeSort(rightArray));
}
```

完整程式碼：
```javascript
function mergeSort(arr) {
  const n = arr.length;
  if (n < 1) { return arr; }
  const midIndex = Math.floor(n / 2);
  const leftArray = arr.slice(0, midIndex);
  const rightArray = arr.slice(midIndex);
  return merge(mergeSort(leftArray), mergeSort(rightArray));

  function merge(leftArr, rightArr) {
    const temp = [];
    let [leftIndex, rightIndex] = [0, 0];
    while (leftIndex < leftArr.length && rightIndex < rightArr.length) {
      if (leftArr[leftIndex] < rightArr[rightIndex]) {
        temp.push(leftArr[leftIndex++]);
      } else {
        temp.push(rightArr[rightIndex++]);
      }
    }
    return [...temp, ...leftArr.slice(leftIndex), ...rightArr.slice(rightIndex)];
  }
}
```

這種實作方式，並不會更改原陣列，會回傳一個排序後的新陣列。

### 4. 優化版本

使用 `slice()` 分割陣列的方式，雖然簡潔好理解，但會額外使用很多記憶體空間，因此我們可以直接在原陣列上進行分割的操作。

要在原陣列上進行分割操作，我們會使用紀錄索引位置的方式。

一共需要傳入 `start` 開頭和 `end` 結尾，再計算出 `mid` 中間位置。
1. 左子陣列為 `start` 至 `mid`
2. 右子陣列為 `mid + 1` 至 `end`

```javascript
// 1
function mergeSort(arr, start = 0, end = arr.length - 1) {
  
  if (start === end) { return arr; } // 2
  
  const mid = Math.floor((start + end) / 2); // 3
  // 4
  mergeSort(arr, start, mid);
  mergeSort(arr, mid + 1, end);
  
  merge(arr, start, mid, end); // 3
  return arr;
}
```
1. `start` 和 `end` 的參數預設值為陣列開頭和結尾。
2. 停止歸遞條件：開頭與結尾索引相同時就停止。
3. 計算中間的索引位置。
4. 歸遞
5. 合併操作

合併操作函式，基本邏輯與原本的差不多，但最後一步不回傳排序好的陣列，而是用記錄下來的索引位置，直接覆蓋掉原陣列：
```javascript
function merge(arr, start, mid, end) {
  let temp = [];
  let [leftIndex, rightIndex] = [start, mid + 1];  // 1

  while (leftIndex <= mid && rightIndex <= end) {
    if (arr[leftIndex] < arr[rightIndex]) {
      temp.push(arr[leftIndex++]);
    } else {
      temp.push(arr[rightIndex++]);
    }
  }
  while (leftIndex <= mid) {
    temp.push(arr[leftIndex++]);
  }
  while (rightIndex <= end) {
    temp.push(arr[rightIndex++]);
  }
  // 3
  for (let i = start; i <= end; i++) {
    arr[i] = temp[i - start];
  }
}
```
1. 左右陣列起始索引。
2. 終止條件：起始索引等於結尾索引。
3. 將排序完的元素，覆蓋掉原陣列。

完整程式碼：
```javascript
function mergeSort(arr, start = 0, end = arr.length - 1) {
  if (start === end) { return arr; } 
  const mid = Math.floor((start + end) / 2);
  mergeSort(arr, start, mid);
  mergeSort(arr, mid + 1, end);
  merge(arr, start, mid, end);
  return arr;

  function merge(arr, start, mid, end) {
    let temp = [];
    let [leftIndex, rightIndex] = [start, mid + 1];
    while (leftIndex <= mid && rightIndex <= end) {
      if (arr[leftIndex] < arr[rightIndex]) {
        temp.push(arr[leftIndex++]);
      } else {
        temp.push(arr[rightIndex++]);
      }
    }
    while (leftIndex <= mid) {
      temp.push(arr[leftIndex++]);
    }
    while (rightIndex <= end) {
      temp.push(arr[rightIndex++]);
    }
    for (let i = start; i <= end; i++) {
      arr[i] = temp[i - start];
    }
  }
}
```
此作法可以減少記憶體的使用（減少空間複雜度），但會更動原陣列。

## 迭代結構實作

當資料數量非常非常大時，歸遞結構的操作很容易導致 **[堆疊溢位](https://zh.wikipedia.org/wiki/%E5%A0%86%E7%96%8A%E6%BA%A2%E4%BD%8D)**，因此可以改用迭代結構。

上面實作完成後，你可以發現，我們可以直接跳過分割這個步驟，直接將它視為已分割後的狀態，直接進行合併操作。

流程：
1. 將陣列中相鄰兩個元素進行合併操作（排序）。
2. 每輪進行操作的元素數量會翻倍成長。
3. 當長度大於等於陣列元素數量，排序完畢。

```javascript
function mergeSort(arr) {
  let n = arr.length;
  for (let i = 1; i <= n; i *= 2) {
    for (let j = 0; j + i < n; j += i * 2) {
      const start = j;
      const end = Math.min(j + i * 2 - 1, n - 1);
      const mid = j + i - 1;
      merge(arr, start, mid, end);
    }
  }
  return arr;
}
```
- `i = 1`：起始元素數量為 1。
- `i <= n`：當元素數量大於等於 `n` 表示排序完畢。 
- `i *= 2`：每輪元素數量會翻倍。
- `j = 0`：從頭開始取元素遍歷。
- `j + i < n`：當索引 `j` + `i` 超過 `n` 表示這輪都合併操作過了。
- `j += i * 2`：一次操作元素的數量會是 `i` 兩倍。
- `end`：若要操作的元素索引大於陣列，就取陣列的最後一個元素。

完整程式碼：
```javascript
function mergeSort(arr) {
  let n = arr.length;

  for (let i = 1; i <= n; i *= 2) {
    for (let j = 0; j + i < n; j += i * 2) {
      const start = j;
      const end = Math.min(j + i * 2 - 1, n - 1);
      const mid = j + i - 1;
      merge(arr, start, mid, end);
    }
  }
  return arr;
  
  function merge(arr, start, mid, end) {
    let temp = [];
    let [leftIndex, rightIndex] = [start, mid + 1];
    while (leftIndex <= mid && rightIndex <= end) {
      if (arr[leftIndex] < arr[rightIndex]) {
        temp.push(arr[leftIndex++]);
      } else {
        temp.push(arr[rightIndex++]);
      }
    }
    while (leftIndex <= mid) {
      temp.push(arr[leftIndex++]);
    }
    while (rightIndex <= end) {
      temp.push(arr[rightIndex++]);
    }
    for (let i = start; i <= end; i++) {
      arr[i] = temp[i - start];
    }
  }
}
```


## 分析

簡易分析，假設資料為 $n$，分割操作可以略過不計。每輪操作的資料數量固定為 $n$，共會執行 $\log_{2}n$ 輪合併操作，整體執行時間約為 $n \log_{2}n$。

- 穩定排序
- 時間複雜度
  - 最佳：$O(n \log n)$
  - 最差：$O(n \log n)$
  - 平均：$O(n \log n)$
- 空間複雜度為： $O(n)$

