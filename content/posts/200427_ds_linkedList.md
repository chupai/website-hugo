---
title: "JavaScript 學演算法（五）- 鏈結串列 Linked list "
date: 2020-04-27
description: "這週是六角鼠年鐵人賽第十三週，要來簡單說明與陣列類似的資料結構「鏈結串列 Linked list」。"
keywords: ["演算法 Algorithm", "資料結構", "JavaScript 學演算法","竹白"]
tags: ["資料結構","鏈結串列", "w3HexSchool"]
series: ["JavaScript 學演算法"]
categories: ["w3HexSchool六角鼠年鐵人賽"]
image: images/covers/200427_ds_linkedList.png
draft: false
libraries:
- katex
---

這週是 六角鼠年鐵人賽 第十三週，要來簡單說明與陣列類似的資料結構「鏈結串列 Linked list」。

<!--more-->
{{< featuredImage >}}

## 前言

首先來談談陣列的缺點，在大多數的程式語言中，陣列的長度（大小）宣告後就是固定的，也就是說，宣告時已經決定最大空間，如果預設的空間太小容易發生上溢，如果太大容易造成記憶體空間浪費。

而且從陣列的開頭或中間插入、刪除元素時，往往需要般動其他元素，因此效率不佳。

## 鏈結串列是什麼

**鏈結串列（Linked list）**，中國稱「链表」，與陣列一樣都是線性資料結構，但不同於陣列，它為鏈式儲存結構，也就是記憶體位置儲存為不連續性。

### 1. 定義

鏈結串列是由一連串 **節點（node）** 組成，節點之間是透過指標來連接。所以儲存上並不需要連續的空間。

每個節點包含：
- 資料元素
- 指標

指標（又稱鏈結、引用），通常為一或兩個，用來指向上／下個的位置，若沒有上／下一個節點，則為空。如果指標斷裂，資料就遺失。

舉生活上的例子來說，鏈結串列如同火車的車廂一樣，一節拉著一節。

相較於陣列，鏈結串列的元素並不是連續放置的，插入或移除元素不需要移動其他元素，只需要修改上／下個指標的指向。

因為鏈結串列沒有索引，若要存取特定特定，需要從頭開始找起，因此相較於陣列，存取資料較費時。

### 2. 類型

鏈結串列有很多種不同的類型：
- 單向鏈結串列（Singly Linked List）
- 雙向鏈結串列（Doubly Linked List）
- 迴圈鏈結串列（Circularly Linked List）

#### 2.1 單向鏈結串列

單向鏈結串列，又稱單鏈結串列、線性鏈結串列、普通鏈結串列，是最基本的鏈結串列，其特點是鏈結串列的鏈結方向是單向的，對鏈結串列的存取要通過從頭部開始，依序往下讀取。

![單向鏈結串列](https://i.imgur.com/pfSf2FH.png)

#### 2.2 雙向鏈結串列

雙向鏈結串列，又稱為雙鏈結串列，它與單向鏈結串列最大的區別在於，每個結點中都有兩個指標，分別指向上一個和下一個結點。

所以，從雙向鏈結串列中的任意一個結點開始，都可以很方便地存取它的上一個和下一個結點。

![雙向鏈結串列](https://i.imgur.com/z6VJpAr.png)

#### 2.3 迴圈鏈結串列

迴圈鏈結串列，又稱環狀鏈結串列、循環鏈結串列，它與一般的鏈結串列操作基本一致，但串列頭尾的指標會連接再一起，形成一個環。

![](https://i.imgur.com/cZ2SLKs.png)

### 3. 時間複雜度

不管是維基百科還是其他鏈結串列相關文章，都只寫鏈結串列的時間複雜度分別為：
- 插入、刪除：$O(1)$
- 存取、查詢：$O(n)$

但要注意的是，插入與刪除的 $O(1)$ 指的是，已經獲取節點的前提下所執行的動作。沒有獲取節點，要插入或刪除指定位置的節點，會先經過查詢節點這個動作，所以時間複雜度會變為 $O(n)$。

但假如節點位置為開頭，時間複雜度也為 $O(1)$。

### 4. 優缺點比較

鏈結串列與陣列的優缺點比較。

陣列的優點：
- 可利用 `index` 隨機存取（Random Access）只需要 $O(1)$ 的時間。
- 可靠度高，不會因為鏈結斷裂而遺失資料。

陣列的缺點：
- 在開頭或中間插入、刪除元素，需要花費 $O(n)$ 的時間將元素移動。
- 連續的記憶體空間，可能會有用不到的空間，而造成浪費。
- 若陣列已滿，會需要花費 $O(n)$ 的時間搬動資料到新的陣列內。

鏈結串列的優點：
- 資料在記憶體中非連續。
- 插入、刪除元素只需要修改上／下個指標的指向。

鏈結串列的優點：
的缺點：
- 只能順序存取，需要花費 $O(n)$ 的時間。
- 鏈結斷裂就會遺失資料。


## JavaScript 實作鏈結串列

JavaScript 沒有內建鏈結串列，我們使用物件來模擬。

鏈結串列的資料結構：
```javascript
{
  data: 'data1',
  next: {
    data: 'data2',
    next: {
      data: 'data3',
      next: null
    }
  }
}
```

首先會建立兩個類別，分別是 Node 表示節點、LinkedList 提供插入、刪除節點以及其他輔助方法。

### 1. Node

節點 Node 包含資料和指標：
- `data`：資料
- `next`：指向下一個節點。
```javascript
class Node {
  constructor(data) {
    this.data = data;
    this.next = null;
  }
}
```

### 2. LinkedList

基本屬性：
- `head`：指向串列開頭節點
- `tail`：指向串列結尾節點
- `length`：節點數量

```javascript
class LinkedList {
  constructor() {
    this.head = null;
    this.tail = null;
    this.length = 0;
  }
  // methods
}
```

#### 2.1 輔助方法

先建立會用到的輔助方法。

`isEmpty()` 檢查串列是否為空：
```javascript
isEmpty() {
  return this.length === 0;
}
```

`getNode()` 取得指定位置節點：
```javascript
getNode(index) {
  // 判斷指定位置是否在範圍內
  if (index < 0 || index >= this.length) return null;
  // 從頭開始迭代到指定位置訪問
  let currNode = this.head;
  let currIndex = 0;
  while (currIndex < index) {
    currIndex += 1;
    currNode = currNode.next;
  }
  return currNode;
}
```

為了方便驗證，建立一個 `print()` 照順序印出所有節點資料值：
```javascript
print() {
  const temp = [];
  let currNode = this.head;
  while (currNode) {
    temp.push(currNode.data);
    currNode = currNode.next;
  }
  return temp.join(', ');
}
```

#### 2.2 插入新節點

![](https://i.imgur.com/NZZsNT4.png)

- `append(data)`：在串列尾部插入一個節點。
- `insert(index, data)`：在指定位置插入一個節點。

在串列尾部插入一個節點可分為兩種情況：
1. 串列為空，更新 `head` 和 `tail`。
2. 串列不為空，則更新 `tail`。
   - 需要先將原本的 tail 指向新的節點；
   - 再更新 `tail`。

```javascript
append(data) {
  const newNode = new Node(data);
  if (this.isEmpty()) {
    this.head = newNode;
    this.tail = newNode;
  } else {
    this.tail.next = newNode; 
    this.tail = newNode;
  }
  this.length += 1;
}
```

在指定位置插入元素，需要考慮三種情況：
1. 插入串列結尾後面，等同使用 `append` 方法。
2. 插入串列開頭，為取代 `head`。
3. 在中間位置插入，需要修改上一個節點的指標。
   - 取得指定位置的上一個節點；
   - 再取得指定位置上原本的節點；
   - 重新定義上一個節點指標，指向新插入的節點；
   - 最後將新插入的節點指標，指向原本的節點。

```javascript
insert(index, data) {
  if (index >= this.length) {
    this.append(data);
    return;
  }

  const newNode = new Node(data);
  
  if (index <= 0) {
    newNode.next = this.head;
    this.head = newNode;
  } else {
    const prevNode = this.getNode(index - 1);
    const currNode = prevNode.next;
    prevNode.next = newNode;
    newNode.next = currNode;
  }
  this.length += 1;
}
```

驗證：
```javascript
const list = new LinkedList();

list.append('data1');
list.append('data2');
list.append('data3');
console.log(list.print()); // 'data1, data2, data3'

list.insert(0, 'a');
console.log(list.print()); // 'a, data1, data2, data3'
list.insert(4, 'b');
console.log(list.print()); // 'a, data1, data2, data3, b'
list.insert(2, 'b');
console.log(list.print()); // 'a, data1, b, data2, data3, b'
```

#### 2.3 刪除節點

![](https://i.imgur.com/udExKge.png)

先判`index` 有效，刪除指定位置的節點：
1. 刪除的節點是開頭，重新指定 `head`。
2. 其他節點，則找到目標節點，並修改上一個節點的指向，如果目標節點是 `tail`（指標指向 `null`）記得要重新指定。

```javascript
removeAt(index) {
  if (index < 0 || index >= this.length) return;

  if (index === 0) {
    this.head = this.head.next;
  } else {
    const prevNode = this.getNode(index - 1);
    const delNode = prevNode.next;
    const nextNode = delNode.next;
    prevNode.next = nextNode;
    if (nextNode === null) {
      this.tail = prevNode;
    }
  }
  this.length -= 1;
}
```

#### 2.4 其他方法

`getData()` 取得指定位置節點的資料值：
```javascript
get(index) {
  const node = this.getNode(index);
  return node ? node.data : null;
}
```

`indexOf()` 根據資料的值，回傳索引（第一個符合條件的），沒有則回傳 `-1`：
```javascript
indexOf(data) {
  let currNode = this.head;
  let currIndex = 0;
  while (currNode) {
    if (currNode.data === data) return currIndex;
    currIndex += 1;
    currNode = currNode.next;
  }
  return -1;
}
```

`remove()` 根據資料的值，刪除符合的第一個節點：
```javascript
remove(data) {
  const index = this.indexOf(data);
  return this.removeAt(index);
}
```

`clear()` 清空串列：
```javascript
clear() {
  this.head = null;
  this.tail = null;
  this.length = 0;
}
```

`size()` 回傳串列的節點個數：
```javascript
size() {
  return this.length;
}
```


### 3. 最終程式碼

```javascript
class Node {
  constructor(data) {
    this.data = data;
    this.next = null;
  }
}

class LinkedList {
  constructor() {
    this.head = null;
    this.tail = null;
    this.length = 0;
  }

  append(data) {
    const newNode = new Node(data);
    if (this.isEmpty()) {
      this.head = newNode;
      this.tail = newNode;
    } else {
      this.tail.next = newNode;
      this.tail = newNode;
    }
    this.length += 1;
  }

  insert(index, data) {
    if (index >= this.length) {
      this.append(data);
      return;
    }

    const newNode = new Node(data);
    if (index <= 0) {
      newNode.next = this.head;
      this.head = newNode;
    } else {
      const prevNode = this.getNode(index - 1);
      const currNode = prevNode.next;
      prevNode.next = newNode;
      newNode.next = currNode;
    }
    this.length += 1;
  }

  removeAt(index) {
    if (index < 0 || index >= this.length) return;

    if (index === 0) {
      this.head = this.head.next;
    } else {
      const prevNode = this.getNode(index - 1);
      const delNode = prevNode.next;
      const nextNode = delNode.next;
      prevNode.next = nextNode;
      if (nextNode === null) {
        this.tail = prevNode;
      }
    }
    this.length -= 1;
  }

  indexOf(data) {
    let currNode = this.head;
    let currIndex = 0;
    while (currNode) {
      if (currNode.data === data) return currIndex;
      currIndex += 1;
      currNode = currNode.next;
    }
    return -1;
  }

  remove(data) {
    const index = this.indexOf(data);
    return this.removeAt(index);
  }

  getNode(index) {
    if (index < 0 || index >= this.length) return null;
    let currNode = this.head;
    let currIndex = 0;
    while (currIndex < index) {
      currIndex += 1;
      currNode = currNode.next;
    }
    return currNode;
  }

  getData(index) {
    const node = this.getNode(index);
    return node ? node.data : null;
  }

  isEmpty() {
    return this.length === 0;
  }

  clear() {
    this.head = null;
    this.tail = null;
    this.length = 0;
  }

  size() {
    return this.length;
  }

  print() {
    const temp = [];
    let currNode = this.head;
    while (currNode) {
      temp.push(currNode.data);
      currNode = currNode.next;
    }
    return temp.join(', ');
  }
}
```

### 4. 雙向鏈結串列

接下來我們來實作雙向鏈結串列，它與單向鏈結串列的差異在於，節點會有兩個指標。

節點上會多一個指向上一個節點的指標：
```javascript
class DoublyNode {
  constructor(data) {
    this.data = data;
    this.next = null;
    this.prev = null;
  }
}
```

除了插入、刪除節點要多加處理 `prev` 指標，其他方法操作與單向鏈結串列相同，我們這裡直接繼承 LinkedList：

```javascript
class DoublyLinkedList extends LinkedList {
  append(data) {
    const newNode = new DoublyNode(data);
    if (this.isEmpty()) {
      this.head = newNode;
      this.tail = newNode;
    } else {
      newNode.prev = this.tail; // new
      this.tail.next = newNode;
      this.tail = newNode;
    }
    this.length += 1;
    return true;
  }
  
  insert(index, data) {
    if (index >= this.length) {
      this.append(data);
      return;
    }

    const newNode = new DoublyNode(data);
    if (index <= 0) {
      this.head.prev = newNode; // new
      newNode.next = this.head;
      this.head = newNode;
    } else {
      const prevNode = this.getNode(index - 1);
      const currNode = prevNode.next;
      prevNode.next = newNode;
      newNode.next = currNode;
    }
    this.length += 1;
  }
  
  removeAt(index) {
    if (index < 0 || index >= this.length) return;

    if (index === 0) {
      this.head = this.head.next;
      this.head.prev = null; // new
    } else {
      const prevNode = this.getNode(index - 1);
      const delNode = prevNode.next;
      const nextNode = delNode.next;
      prevNode.next = nextNode;
      if (nextNode === null) {
        this.tail = prevNode;
      } else {
        nextNode.prev = prevNode; // new
      }
    }
    this.length -= 1;
  }
}
```

### 5. 迴圈鏈結串列

迴圈鏈結串列 `tail` 的 `next` 指標會指向 `head`、`head` 的 `prev` 指標會指向 `tail`，而非 `null`。


## LeetCode 相關題目

- [707. Design Linked List 設計鏈結串列](https://leetcode.com/problems/design-linked-list/)
- [141. Linked List Cycle 迴圈鏈結串列](https://leetcode.com/problems/linked-list-cycle/)
- [206. Reverse Linked List 反轉鏈結串列](https://leetcode.com/problems/reverse-linked-list/) 

## 總結

陣列是 JavaScript 最常使用的資料結構，JavaScript 的陣列與其它語言相比很特殊，沒有元素型別與大小限制，但將元素從開頭插入或移除，時間複雜度一樣需要 $O(n)$，而鏈結串列只需要 $O(1)$。

如果沒有頻繁存取元素的需求，而有需要頻繁將元素插入開頭，可以考慮使用鏈結串列的資料結構邏輯。