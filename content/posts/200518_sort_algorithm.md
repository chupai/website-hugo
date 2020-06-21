---
title: "JavaScript 學演算法（八）- 排序演算法"
date: 2020-05-18
description: "排序演算法（Sort Algorithm）是基礎演算法之一，是一種能將一串資料依照特定排序方式進行排列的一種演算法。"
keywords: ["演算法 Algorithm", "排序演算法", "JavaScript 學演算法","氣泡排序","插入排序", "選擇排序","竹白"]
tags: ["排序演算法","氣泡排序","插入排序", "選擇排序", "w3HexSchool"]
series: ["JavaScript 學演算法"]
categories: ["w3HexSchool六角鼠年鐵人賽"]
image: images/covers/200518_sort_algorithm.png
draft: false
libraries:
- katex
---

這週是六角鼠年鐵人賽第十六週，資料結構先暫停一下，換換口味，來看排序。排序演算法（Sort Algorithm）是基礎演算法之一，是一種能將一串資料依照特定排序方式進行排列的一種演算法。

<!--more-->

{{< featuredImage >}}

## 什麼是排序（Sort）

### 1. 定義

**排序（Sort）**，將一組資料一使用者需求，予以重新排列其順序。

一般會依資料之大小順序排序（由大至小、或由小至大）。

### 2. 分類

排序演算法分類方式有以下這幾種。

第一類：內部與外部
- **內部排序（Internal Sort）** 又稱「陣列排序」
  - 定義：排序之工作，主要在主記憶體（RAM）完成。
  - 意指資料量小，可以直接放在記憶體內進行。
- **外部排序（External Sort）** 又稱「檔案排序」
  - 定義：排序之工作，主要是在輔助記憶體（Disk, File）完成。
  - 意指資料量大，無法直接存放在記憶體，必須先存放於輔助記憶體內再處理。

第二類：穩定性與不穩定性
- **穩定性（Stable）**：相同值的資料，排序後順序和排序前一樣。
- **不穩定性（Unstable）**：相同值的資料，排序後順序不一定和排序前一樣。

第三類：簡單與高等
- **簡單排序**：排序演算法簡單，但執行時間較長。
- **高等排序**：排序演算法複雜，執行時間較短。

第四類：比較與非比較
- **比較排序**：通過比較來決定元素間的相對次序。
- **非比較排序**：不通過比較來決定元素間的相對次序。


### 3. 常見排序演算法

排序相關動畫影片，可以稍微的看一下：
- [如何最有效率地整理書架？ - Chand John](https://youtu.be/WaNLJf8xzC4)
- [15 Sorting Algorithms in 6 Minutes](https://www.youtube.com/watch?v=kPRA0W1kECg)


簡單排序：
- 氣泡排序（Bubble Sort）
- 選擇排序（Selection Sort）
- 插入排序法（Insertion Sort）

初學排序所必須知道的三個最基本排序演算法，但它們由於速度不快而不實用。

高等排序：
- 合併排序 Merge Sort
- 快速排序 Quick Sort
- 希爾排序 Shell Sort
- 堆積排序 Heap Sort

高等排序通常用於高效處理大量資料（幾萬起跳），不過有些對於資料過於小（幾千幾百）可能反而比簡單排序還慢。


## 簡單排序

### 1. 氣泡排序（Bubble Sort）

**氣泡排序（Bubble Sort）**又稱為**冒泡排序**、**泡沫排序**，是排序演算法中最簡單的，但運行時間是最差的。

其排序方式為相臨資料反覆進行比較，若發現資料順序不對，就將資料互換。資料將如氣泡般，從一端漂浮到另一端。

流程模擬動畫：
<iframe height="500" style="width: 100%;" scrolling="no" title="Sort Algorithm - Bubble Sort with Vue.js" src="https://codepen.io/chupai/embed/vYNeREb?height=265&theme-id=dark&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" loading="lazy">
  See the Pen <a href='https://codepen.io/chupai/pen/vYNeREb'>Sort Algorithm - Bubble Sort with Vue.js</a> by Chupai@Design
  (<a href='https://codepen.io/chupai'>@chupai</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

遞增排列運作原理：
- 第一輪
比較相鄰的元素，如果前者大於後者就相互交換；
從頭到尾執行比較動作，可以確保最後一個元素將會是最大的。
- 第二輪
扣除最後一個元素（已排序完畢元素），針對其餘元素重複上述步驟；

每輪執行比較的元素會越來越少，執行至剩一個元素，排序完畢。

氣泡排序的過程範例：
```
nums = [4, 2, 3, 1];

第一輪
4 2 交換 => 2 4 3 1
4 3 交換 => 2 3 4 1
4 1 交換 => 2 3 1 4

第二輪
2 3 不換 => 2 3 1 4
3 1 交換 => 2 1 3 4

第三輪
2 1 交換 => 1 2 3 4
```

氣泡排序分析：
- 穩定排序
- 時間複雜度
  - 最佳：$O(n)$
  - 最差：$O(n^2)$
  - 平均：$O(n^2)$
- 空間複雜度為： $O(1)$

演算法實作：
```javascript
function bubbleSort (arr) {
  
  const n = arr.length; // 1
  // 2
  for(let i = 0; i < n - 1; i++) {
    // 3
    for(let j = 0; j < n - 1 - i; j++) {
      // 4
      if(arr[j] > arr[j + 1]) {
        // 5
        const temp = arr[j];
        arr[j] = arr[j + 1];
        arr[j + 1] = temp;
      }
    }
  }
  return arr;
}
```
1. 元素數量。
2. 執行 `n - 1` 輪（最後一次只剩自己，所以不比較）。
3. 交換 `n - 1 - i` 次（兩兩互相比較，所以 -1，並扣除每輪已經排好的元素，所以減 `i`）。
4. 比較大小。
5. 元素交換位置。

交換位置也可以使用 ES6 的解構指定（destructuring assignment）：
```javascript
[arr[j], arr[j+1]] = [arr[j+1], arr[j]];
```

你可以建立一個交換函式，之後很多排序法都會用到：
```javascript
function swap(arr, index1, index2) {
  const temp = arr[index1];
  arr[index1] = arr[index2];
  arr[index2] = temp;
  
  // - ES6 的方式
  // [array[index1], array[index2]] = [array[index2], array[index1]];
}
```

氣泡排序的平均與最差的時間複雜度都是 $O(n^2)$，但最佳的情況（輸入陣列已是排序好的）時間複雜度為 $O(n)$（元素不做任何的交換）。

要讓最佳的情況時間複雜度為 $O(n)$，需要優化一下程式：
```javascript
function bubbleSort (arr) {
  const n = arr.length;
  let swapped = true;
  
  for(let i = 0; (i < n - 1) && swapped; i++) {
    swapped = false;
    for(let j = 0; j < n - 1 - i; j++) {
      if(arr[j] > arr[j + 1]) {
        swapped = true;
        swap(arr, j, j + 1);
      }
    }
  }
  return arr;
}
```
加上一個標注（flag），在新的一輪開始時，判斷上一輪是否有發生交換，如果沒有表示剩餘的元素已經排序完畢，下一輪會因為 `swapped` 被設為 `false` 就會跳出迴圈。

### 2. 選擇排序（Selection Sort）

**選擇排序（Selection Sort）** 是一種簡單直觀的排序演算法，類似氣泡排序。

其排序方式為反覆進行搜數列中的最小值，並與最左側的資料交互。

流程模擬動畫：
<iframe height="500" style="width: 100%;" scrolling="no" title="Sort Algorithm - Selection Sort with Vue.js" src="https://codepen.io/chupai/embed/QWjaWVE?height=265&theme-id=dark&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/chupai/pen/QWjaWVE'>Sort Algorithm - Selection Sort with Vue.js</a> by Chupai@Design
  (<a href='https://codepen.io/chupai'>@chupai</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

遞增排列運作原理：
- 第一輪
預設第一個元素為最小值，往右側進行搜尋最小值；
若遇到比目前最小值還小的元素就替換掉；
搜尋至最後，將最小值與最左側元素交換位置。
- 第二輪
扣除第一個元素，針對其餘元素重複以上的步驟。

持續上述步驟，每輪執行比較的元素會越來越少，直到排序完畢。

選擇排序的過程範例：
```
nums = [4, 1, 3, 2];

第一輪
min 4 (預設)
min 1
交換位置 => 1 4 3 2

第二輪
min 4 (預設)
min 3
min 2
交換位置 => 1 2 4 3

第三輪
min 4 (預設)
min 3
交換位置 => 1 2 3 4
```

選擇排序分析：
- 不穩定排序。
- 時間複雜度
  - Best Case：$O(n^2)$
  - Worst Case：$O(n^2)$
  - Average Case：$O(n^2)$
- 空間複雜度為： $O(1)$

演算法實作：
```javascript
function selectionSort(arr) {
  const n = arr.length; // 1
  // 2
  for(let i = 0; i < n; i++) {
    // 3
    let minIndex = i;
    // 4
    for(let j = 1 + i; j < n; j++) {
      // 5
      if(arr[j] < arr[minIndex]) {
        minIndex = j;
      }
    }
    swap(arr, i, minIndex); // 6
  }
  return arr;
}
```
1. 元素數量。
2. 執行 n 輪。
3. `minIndex` 用來紀錄最小值的索引。
4. 因為 `minIndex` 預設為第一元素 `j` 從 `1` 開始尋找，每輪會扣除已排好的元素，所以加 `i`。
5. 比較是否為最小值。
6. 一輪結束後就將最小值與 `i` 交換。

選擇排序就算陣列已經是排序好的狀態，還是要經過多輪比較，所以不論是最好、最壞、平均的時間複雜度皆為 $O(n^2)$。但跟氣泡排序比起來，執行交換操作只需要 `n` 次。

### 3. 插入排序（Insertion Sort）

插入排序（Insertion Sort）是也一種簡單直觀的排序演算法，你如果玩過撲克牌，就都有用過。

其排序方式為，將左側作為已排序數列，右側為未排序數列，從未排序的數列中取第一個，放到已排序數列中的適當位置。

流程模擬動畫：
<iframe height="500" style="width: 100%;" scrolling="no" title="Sort Algorithm - Insertion Sort with Vue.js" src="https://codepen.io/chupai/embed/WNQdarJ?height=265&theme-id=dark&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/chupai/pen/WNQdarJ'>Sort Algorithm - Insertion Sort with Vue.js</a> by Chupai@Design
  (<a href='https://codepen.io/chupai'>@chupai</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

遞增排列運作原理：
- 第一輪
從第一個元素開始，該元素視為已經被排序；
取出下一個元素，判斷要插入左側元素的前面還是後面。
- 第二輪
已經被排序已有兩個元素；
取出下一個元素，判斷要插入左側元素的前面還是後面；
如果是前面就依序往下判斷。

持續上述步驟，右側位被排序的元素會遞減，直到排序完畢。

插入排序的過程範例：
```
nums = [4, 2, 3, 1];

第一輪
[4] [2 3 1]
[  4] 2
[2 4]

第二輪
[2 4] [3 1]
[2   4] 3
[2 3 4]

第三輪
[2 3 4] [1]
[2 3   4] 1
[2   3 4] 1
[  2 3 4] 1
[1 2 3 4]
```

分析：
- 穩定排序。
- 時間複雜度
  - Best Case：$O(n)$
  - Worst Case：$O(n^2)$
  - Average Case：$O(n^2)$
- 空間複雜度為： $O(1)$

演算法實作：
```javascript
function insertionSort(arr) {
  const n = arr.length;  // 1
  // 2
  for (let i = 1; i < n; i++) {
    let temp = arr[i]; // 3
    let j = i; // 4
    // 5
    while (j > 0 && temp < arr[j - 1]) {
      arr[j] = arr[j - 1];  // 6
      j -= 1;
    }
    arr[j] = temp;  // 7
  }
  return arr;
}
```
1. 元素數量。
2. 從第二個元素開始，所以 `i = 1`。
3. 使用一個變數儲存要判斷元素的值。
4. 使用一個變數儲存前要判斷元素的當前索引。
5. 只要當前索引比 0 大，並且小於左側的值，就繼續比較。
6. 將左側的值往後移。
7. 將要判斷元素的值插入正確位置上。

### 4. 總結

這三種簡單演算法複雜度基本上差不多：

| 排序方法 | 最佳時間 | 最差時間 | 平均時間 | 空間 | 穩定性 |
| --- | --- | --- | --- | --- | --- |
| 氣泡 Bubble | $O(n)$ | $O(n^2)$ | $O(n^2)$ | $O(1)$ | 穩定 |
| 選擇 Selection | $O(n^2)$ | $O(n^2)$ | $O(n^2)$ | $O(1)$ | 不穩定 |
| 插入 Insertion | $O(n)$ | $O(n^2)$ | $O(n^2)$ | $O(1)$ | 穩定 |

這是一個簡易的執行時間計時器，對 100 ~ 10000 個隨機數字進行排序。
<iframe height="500" style="width: 100%;" scrolling="no" title="Simple Sorting Algorithm  EXECUTION TIME" src="https://codepen.io/chupai/embed/NWGyWVG?height=265&theme-id=dark&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/chupai/pen/NWGyWVG'>Simple Sorting Algorithm  EXECUTION TIME</a> by Chupai@Design
  (<a href='https://codepen.io/chupai'>@chupai</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

執行時間會根據執行環境而有所不同，不過基本上元素數量越小，看不出彼此間的差異，但你可以觀察到當元素達到 10000 時，氣泡排序明顯是最差的，而插入排序稍微快於選擇排序。

不過這三種排序法，資料量非常大時（幾十萬起跳），基本上都要跑很久，所以實際應用上都不適合，下週來看比較實用的合併排序。