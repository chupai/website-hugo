---
title: "JavaScript 學演算法（十九）- 樹堆"
date: 2020-08-03
description: "樹堆（Treap）相較於紅黑樹，可以說是非常簡單的平衡樹。"
keywords: ["演算法 Algorithm", "資料結構", "二元搜尋樹", "二元樹","樹堆", "平衡樹", "Treap", "竹白"]
tags: ["資料結構", "二元搜尋樹", "二元樹","Treap", "平衡樹", "樹堆", "w3HexSchool"]
series: ["JavaScript 學演算法"]
categories: ["w3HexSchool六角鼠年鐵人賽"]
image: images/covers/200803_ds_treap.png
draft: false
libraries:
- katex
---

這週是六角鼠年鐵人賽第二十七週。

我們來看相較於 **紅黑樹**，可以說是非常簡單的平衡樹：**樹堆（Treap）**。

<!--more-->
{{< featuredImage >}}

## 樹堆

**樹堆（Treap）** 其名稱是 Tree 和 Heap 的結合。顧名思義，Treap 同時滿足二元搜尋樹和 Heap（堆積）的性質。屬於 **<a href="https://zh.wikipedia.org/wiki/%E7%AC%9B%E5%8D%A1%E5%B0%94%E6%A0%91" target="_blank">笛卡爾樹（Cartesian Tree）</a>** 的一種，兩者在結構上是相同的，只是應用不同。


Treap 結構相當於以隨機資料插入的二元搜尋樹，相較於其它平衡樹，實現可以說是非常簡單直觀。由於樹的結構並不等於添加元素順序，因此可有效避免一般的二元搜尋樹所出現的最糟情況。

### 1. 性質

Treap 在二元搜尋樹的基礎上，每個節點都有一個隨機的 **優先順序（priority）**。

1. Treap 為一棵二元搜尋樹；
2. 左、右子樹也是 Treap；
3. 每個節點都有一個隨機的 **優先順序**；
4. **優先順序** 保持著 Heap 性質。
   - Max Heap（最大堆積），所有的父節點都比子節點要大；
   - Min Heap（最小堆積），所有的父節點都比子節點要小。

需要注意的兩點：
- Treap 相較於 Heap，不需要滿足 Complete binary tree（完全二元樹）的條件；
- Treap 相較於 AVL-Tree，屬於非嚴格平衡樹。

### 2. 新增操作

新增時，會給節點隨機分配一個**優先順序**，新增操作和一般二元搜尋樹相同，新增節點後，需要維護 Heap 的性質，會用到**旋轉操作**。

以 **Max Heap** 為例：
1. 新增節點到一個葉子節點上；
2. 當前節點的**優先順序**比其根大，對根旋轉：
   - 如果當前節點是根的左子節點，右旋轉；
   - 如果當前節點是根的右子節點，左旋轉。

### 3. 刪除操作

因為 Treap 滿足 Heap 性質，所以只需要把要刪除的節點旋轉到葉子節點上，然後直接刪除就可以了。

具體的方法：
1. 比較被刪除節點的左、右子節點**優先順序**：
  　- 左子樹優先順序大，執行右旋轉；
  　- 右子樹優先順序大，執行左旋轉。
2. 直到被刪除節點被旋轉到了葉子節點，然後直接刪除。


## JavaScript 實作 Treap

### 1. 基本結構

我們簡單的寫一個隨機函式，用來模擬隨機分配的優先順：
```javascript
function random() {
  return Math.floor(Math.random() * 1000);
}
```

節點：
```javascript
class TreapNode {
  constructor(data, priority = random()) {
    this.data = data;
    this.priority = priority;
    this.left = null;
    this.right = null;
  }
}
```
節點比一般二元樹多一個**優先順序**。

本體：
```javascript
class Treap {
  constructor() {
    this.root = null;
  }
  // methods
}
```

### 2. 旋轉操作

與 AVL-Tree 相同。

```javascript
rightRotation(node) {
  const temp = node.left;
  node.left = temp.right;
  temp.right = node;
  return temp;
}

leftRotation(node) {
  const temp = node.right;
  node.right = temp.left;
  temp.left = node;
  return temp;
}
```

### 3. 新增操作

```javascript
insert(data) {
  const insertHelper = (node) => {
    let curNode = node;
    if (!curNode) {
      return new TreapNode(data);
    }
    if (data < curNode.data) {
      curNode.left = insertHelper(curNode.left);
      if (curNode.left.priority > curNode.priority) {
        curNode = this.rightRotation(curNode);
      }
    } else if (data > curNode.data) {
      curNode.right = insertHelper(curNode.right);
      if (curNode.right.priority > curNode.priority) {
        curNode = this.leftRotation(curNode);
      }
    }
    return curNode;
  };
  this.root = insertHelper(this.root);
}
```

### 4. 刪除操作

因為節點是單向的，所以我們要記錄父節點和一個 `key` 紀錄當前節點是左、右子樹。

```javascript
remove(data) {
  let curNode = this.root;
  let parentNode = null;
  let key = '';

  // 尋找刪除節點
  while (curNode) {
    if (data < curNode.data) {
      parentNode = curNode;
      key = 'left';
      curNode = curNode.left;
    } else if (data > curNode.data) {
      parentNode = curNode;
      key = 'right';
      curNode = curNode.right;
    } else {
      break;
    }
  }

  // 旋轉刪除節點，直到最底下
  while (curNode) {
    // 判斷節點狀態
    const result = this.comparison(curNode);

    if (result === -1) {
      this.setNode(parentNode, null, key);
      return;
    }
    if (result === 1) {
      curNode = this.rightRotation(curNode);
      this.setNode(parentNode, curNode, key);
      key = 'right';
      parentNode = curNode;
      curNode = curNode.right;
    } else {
      curNode = this.leftRotation(curNode);
      this.setNode(parentNode, curNode, key);
      key = 'left';
      parentNode = curNode;
      curNode = curNode.left;
    }
  }
}
```

輔助函式：
```javascript
setNode(parent, node, key) {
  if (!parent) {
    this.root = node;
  } else {
    parent[key] = node;
  }
}
```

```javascript
comparison(node) {
  if (!node.left && !node.right) {
    return -1;
  }
  if (node.left && !node.right) {
    return 1;
  }
  if (!node.left && node.right) {
    return 0;
  }
  if (node.left.priority > node.right.priority) {
    return 1;
  }
  return 0;
}
```

<iframe height="800" style="width: 100%;" scrolling="no" title="Treap with Vue.js" src="https://codepen.io/chupai/embed/BajdYrW?height=265&theme-id=dark&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/chupai/pen/BajdYrW'>Treap with Vue.js</a> by Chupai@Design
  (<a href='https://codepen.io/chupai'>@chupai</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>
