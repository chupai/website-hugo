---
title: "JavaScript 學演算法（六）- 堆疊 ＆ 佇列"
date: 2020-05-04
description: "這週是六角鼠年鐵人賽第十四週，讓我們來看只關注頭尾的資料結構「堆疊」和「佇列」。"
keywords: ["演算法 Algorithm", "資料結構", "JavaScript 學演算法","竹白"]
tags: ["資料結構","堆疊","佇列", "w3HexSchool"]
series: ["JavaScript 學演算法"]
categories: ["w3HexSchool六角鼠年鐵人賽"]
image: images/covers/200504_ds_stack_queue.png
draft: false
---

這週是 六角鼠年鐵人賽 第十四週，讓我們來看只關注頭尾的資料結構「堆疊」和「佇列」。

<!--more-->
{{< featuredImage >}}

## 堆疊（Stack）

堆疊（Stack），中國稱「栈」，是一種 **後入先出（LIFO, Last In First Out）** 的資料結構。

### 1. 後入先出 LIFO

在 LIFO 的資料結構中，將優先處理最晚加進來的元素（最新）。

堆疊使用兩種基本操作：
- push：將資料放入堆疊頂端（新增）
- pop：拿出疊頂端的資料（刪除）


簡單來說，就是先進去資料的最後出來、最後進去的資料最先出來。可以將堆疊想像成疊盤子，不論放盤子或取盤子，都只能從最上方，最先拿的盤子一定是最後一個放上來的。

也就是說，在堆疊中，越底層的資料越舊，越靠近頂層越新，而且只能讀取最上層的資料（最新的）。

![](https://i.imgur.com/ZIODRhb.png)

### 2. JavaScript 實作堆疊

大部分的程式語言都提供內置的堆疊，但 JavaScript 沒有，所以我們用陣列來實作一個。

#### 2.1 宣告類別

宣告一個 Stack 類別：
```javascript
class Stack {
  constructor() {
    this.items = [];
  }
  // methods
}
```
我們需要一個能夠存儲堆疊裡的元素的資料結構，因此我們可以使用陣列來實作。

#### 2.2 屬性方法

主要方法：
- `push(element(s))`：新增一個或多個元素到堆疊頂部。
- `pop()`：移除堆疊頂部元素，同時回傳被移除的元素。

為了符合 LIFO 原則，我們使用陣列的 `push` 和 `pop` 方法實作。
```javascript
push(element) {
  this.items.push(element);
}

pop() {
  return this.items.pop();
}
```

輔助工具：
- `peek()` 或 `top()`：僅回傳堆疊頂端的元素。
- `isEmpty()`：檢查堆疊內是否有元素，是空的回傳 `true`，否則回傳 `false`。
- `clear()`：移除堆疊內的所有元素。
- `size()`：回傳堆疊裡的元素個數，也就是資料長度。

```javascript
// 獲取最後一個元素
peek() {
  return this.items[this.items.length - 1];
}

// 判斷儲存堆疊資料的陣列長度是否為 0
isEmpty() {
  return this.items.length === 0;
}

// 最簡單的清空，給一個新的空陣列
clear() {
  this.items = [];
}

// 回傳儲存堆疊資料的陣列長度
size() {
  return this.items.length;
}
```

最終程式碼如下：
```javascript
class Stack {
  constructor() {
    this.items = [];
  }
  
  push(element) {
    this.items.push(element);
  }

  pop() {
    return this.items.pop();
  }

  peek() {
    return this.items[this.items.length - 1];
  }

  isEmpty() {
    return this.items.length === 0;
  }

  clear() {
    this.items = [];
  }

  size() {
    return this.items.length;
  }
}
```

#### 2.3 建立實體

```javascript
const stack1 = new Stack();

// 新增元素
stack1.push('盤子1');
stack1.push('盤子2');

// 印出長度
console.log(stack1.size()); // 2

// 移除最上方元素
console.log(stack1.pop());  // '盤子2'

// 最上方元素
console.log(stack1.peek()); // '盤子1'

// 清除
stack1.clear();

// 是否為空
console.log(stack1.isEmpty()); // true
```

#### 2.4 基於物件的堆疊

除了使用陣列來儲存堆疊元素，也可以改用物件來儲存堆疊裡的元素。
```javascript
class Stack {
  constructor() {
    this.items = {};
    this.count = 0;
  }

  push(element) {
    this.items[this.count] = element;
    this.count += 1;
  }

  pop() {
    if (this.isEmpty()) {
      return undefined;
    }
    this.count -= 1;
    const result = this.items[this.count];
    delete this.items[this.count];
    return result;
  }

  isEmpty() {
    return this.count === 0;
  }

  clear() {
    this.items = {};
    this.count = 0;
  }

  size() {
    return this.count;
  }
}
```

## 佇列（Queue）

佇列（Queue），中國稱「队列」，特性是 **先進先出（FIFO, First-In-First-Out）** 的資料結構。

### 1. 先入先出 FIFO

在 FIFO 的資料結構中，會優先處理最早加進來的元素（最舊）。

佇列使用兩種基本操作：
- enqueue：由佇列的後端（Rear / Back）添加新資料。
- dequeue：由佇列的前端（Front）移除資料。

舉個生活上的例子，排隊上廁所：先到的人先上，後來的排後面。

![](https://i.imgur.com/B0bWPbJ.png)

當你想要按順序處理元素時，使用佇列會是一個很好的選擇。

### 2. JavaScript 實作佇列

大部分的程式語言都提供內置的佇列，但 JavaScript 沒有，所以我們用陣列來實作一個。

#### 2.1 宣告類別

宣告一個 Queue 類別：
```javascript
class Queue {
  constructor() {
    this.items = [];
  }
  // methods
}
```
並用陣列來儲存佇列裡的元素。

#### 2.2 屬性方法

佇列的兩種主要操作方法：
- `enqueue(element(s))`：新增一個或多個元素到佇列尾部。
- `dequeue()`：移除佇列的第一個元素，並回傳被移除的元素。

```javascript
enqueue(element) {
  this.items.push(element);
}

dequeue() {
  return this.items.shift();
}

```
輔助工具：
- `front()` 或 `head()`：僅回傳佇列的第一個元素。
- `rear()` 或 `tail()`：僅回傳佇列的最一個元素。
- `isEmpty()`：檢查佇列內是否有元素，是空的回傳 `true`，否則回傳 `false`。
- `clear()`：移除佇列內的所有元素。
- `size()`：回傳佇列裡的元素個數，也就是資料長度。

```javascript
front() {
  return this.items[0];
}

rear() {
  return this.items[this.items.length - 1];
}

isEmpty() {
  return this.items.length === 0;
}

clear() {
  this.items = [];
}

size() {
  return this.items.length;
}
```

最終程式碼如下：
```javascript
class Queue {
  constructor() {
    this.items = [];
  }

  enqueue(element) {
    this.items.push(element);
  }

  dequeue() {
    return this.items.shift();
  }

  front() {
    return this.items[0];
  }

  rear() {
    return this.items[this.items.length - 1];
  }
  
  isEmpty() {
    return this.items.length === 0;
  }

  clear() {
    this.items = [];
  }

  size() {
    return this.items.length;
  }
}
```

#### 2.3 建立實體

模擬排隊：
```javascript
const queue = new Queue();

// 隊伍空的嗎?
console.log(queue.isEmpty()); // ture

// 排隊
queue.enqueue('A');
queue.enqueue('B');
queue.enqueue('C');

// 先出隊的
console.log(queue.dequeue()); // 'A'

// 剩幾個?
console.log(queue.size()); // 2

// 隊伍最後一個
console.log(queue.rear()); // 'C'
```

### 3. 優先佇列（Priority Queue）

在一般情況下，從佇列中處理（刪除）的一定是第一個加入的元素，但優先佇列（Priority Queue），不必遵守佇列 FIFO 的特性，每個元素都會設定優先級，優先級最高（數字越小越高）的會優先處理，若相同級別才看排列順序。

用排隊來比喻就是 VIP 會員等級，等級越高優先服務，或是醫院急診室會評估傷患等級來給予治療順序。

優先佇列的差異在於 `enqueue` 方法，在加入元素時，會根據優先級決定插入的位置。

1. `priority` 為優先級，預設值給它無限大。
2. 我們用一個 `queueElement` 物件來儲存元素和優先級的值。
3. 如果佇列為空直接入列，否則比較優先級。
4. 一個一個比較元素優先級：
   - 優先級較高就用插入在它的前面，並結束函式；
   - 如果都沒有就加到佇列後端。

```javascript
class Priorityqueue extends Queue {
  enqueue(element, priority = Infinity) {
    const queueElement = { element, priority };
    if (this.isEmpty()) {
      this.items.push(queueElement);
    } else {
      for (let i = 0; i < this.items.length; i++) {
        if (priority < this.items[i].priority) {
          this.items.splice(i, 0, queueElement);
          return;
        }
      }
      this.items.push(queueElement);
    }
  }
}
```

建立實體：
```javascript
const pq = new Priorityqueue();

pq.enqueue('A', 1);
pq.enqueue('B');
pq.enqueue('C', 1);
pq.enqueue('D', 2);

console.log(pq.dequeue());  // { element: 'A', priority: 1 }
console.log(pq.dequeue());  // { element: 'C', priority: 1 }
console.log(pq.dequeue());  // { element: 'D', priority: 2 }
console.log(pq.dequeue());  // { element: 'B', priority: Infinity }
```

## 堆疊的應用

### 1. 十進制轉二進制

十進制轉二進制轉換過程：
- 10 / 2 = 5 餘 0
- 5 / 2 = 2 餘 1
- 2 / 2 = 1 餘 0
- 1 / 2 = 0 餘 1
- 10 的二進制結果為 1010

```javascript
function divideBy2(decNumber) {
  const remStack = new Stack();
  let number = decNumber;
  let rem = null;        // 餘數

  // 將餘數放進堆疊中
  while (number > 0) {
    rem = Math.floor(number % 2);
    remStack.push(rem);
    number = Math.floor(number / 2);
  }

  // 將結果連成字串
  let baseString = '';
  while (!remStack.isEmpty()) {
    baseString += remStack.pop();
  }

  return baseString;
}
```

將上面程式碼稍微修改，可以改成十進制轉 2 ~ 36 進制，10 ~ 35 用英文字母表示。

```javascript
function baseConverter(decNumber, base) {
  if (!(base >= 2 && base <= 36)) { return ''; }
  
  const remStack = new Stack();
  const DIGITS = '0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ';
  let number = decNumber;
  let rem = null;

  while (number > 0) {
    rem = Math.floor(number % base);
    remStack.push(rem);
    number = Math.floor(number / base);
  }

  let baseString = '';
  while (!remStack.isEmpty()) {
    baseString += DIGITS[remStack.pop()];
  }
  return baseString;
}
```

### 2. 驗證括號的問題

>[Leetcode: 20. Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)
驗證括號（Valid Parentheses）或稱 平衡括號（Balanced Brackets）。

給定一個包括 `(`、`{`、`[`、`)`、`}`、`]` 的字串，判斷字串是否有效。

有效字串需滿足：
- 左括號必須用相同類型的右括號閉合。
- 左括號必須以正確的順序閉合。

注意空字串可被認為是有效字串。

範例：
```javascript
'()'       // ture
'()[]{}'   // true
'(]'       // false
'([)]'     // false
'{[]}'     // true
```

流程：
- 檢查字串，遇到 `( { [` 就放到堆疊中
- 若遇到 `) ] }` 就去找堆疊頂端的資料是否有相對應的 `( { [`
  - 沒有就回傳 `false`
  - 若有就將堆疊頂端對應的 `( { [` 移除
- 最後檢查堆疊內使否為空
  - 如果是空的回傳 `true`
  - 不是空的，表示有沒對應到的 `( { [` 回傳 `false`

```javascript
function isValid(s) {
  const stack = [];
  const OPENING = { '(': 0, '[': 1, '{': 2 };
  const CLOSEING = { ')': 0, ']': 1, '}': 2 };

  for (let i = 0; i < s.length; i++) {
    const character = s[i];
    if (OPENING[character] >= 0) {
      stack.push(character);
    } else if (CLOSEING[character] >= 0) {
      const peek = stack[stack.length - 1];
      if (OPENING[peek] === CLOSEING[character]) {
        stack.pop();
      } else {
        return false;
      }
    }
  }
  return stack.length === 0;
}
```

## 總結

堆疊和佇列這類最基本的資料結構，它們因為特殊條件限制，只關心頭尾的新增、刪除、獲取的操作，並不關心內部的元素的狀態。