---
title: "JavaScript 學演算法（一）- 何謂演算法？"
date: 2020-04-04
description: "這週是 六角鼠年鐵人賽 第九週，接下來幾週大概用記錄我學習演算法的筆記，既然是學前端，當然就是使用 JavaScript。"
keywords: ["演算法 Algorithm", "JavaScript 學演算法","竹白"]
tags: ["演算法 Algorithm", "w3HexSchool"]
series: ["JavaScript 學演算法"]
categories: ["w3HexSchool六角鼠年鐵人賽"]
image: images/covers/200404_algorithm.png
draft: false
---

這週是 六角鼠年鐵人賽 第九週，接下來幾週大概是我學習演算法筆記，既然是學前端，當然就是使用 JavaScript 來實作。

<!--more-->
{{< featuredImage >}}

## 何謂演算法？

**演算法**，英文名稱 **Algorithm**，中國稱為 **算法**。

>演算法，簡單來就是一套解決問題的方法。

要簡單明瞭何謂演算法，推薦觀看這個影片：[人腦也可以執行演算法 - David J. Malan](https://www.youtube.com/watch?v=6hfOvs8pY1k&feature=emb_title)。

### 1. 演算法與程式

演算法通常是電腦所執行的，但演算法在電腦誕生之前就存在，演算法是數學家所提出來的。

電腦看起來很聰明，但其實電腦只會做 0 跟 1 的運算，而我們就是透過 **程式**，把現實世界需要解決的問題變成數學問題，丟給電腦來幫我們做運算處理，而這個解題的方法就是演算法。

所以說，演算法是用以執行計算或完成作業的程序，所有步驟都要能用數學方式表現，而程式是實作演算法的方式。也就是說：
- 演算法相當於「思考程序」
- 程式相當於為了表現演算法的「語言」

### 2. 演算法的表示方式

- 設計程式時，通常會使用 **虛擬碼 pseudocode** 來表示；
- 設計電子電路，會使用 **流程圖** 來表示；
- 而為了理解演算法，會使用文字與圖片來輔助說明。

## 演算法的定義

**演算法** 是對特定問題求解步驟的一種描述，一般演算法嚴謹的條件必須符合五個標準：
1. 輸入（Input）：至少有零個或以上的輸入（可以沒輸入或多個輸入）。
2. 輸出（Output）：至少有一個或以上回傳結果（經過演算法計算獲得）。
3. 確定性/明確性（Definiteness）：每一個指令步驟必須明確而不造成混淆。在一定條件下，相同的輸入只能有唯一結果。
4. 有限性（Finiteness）：在有限步驟後一定會結束（完成或終止），確保不會進入無窮迴圈。
5. 可行性/有效性（Effectiveness）：步驟清楚可行，能使用數學運算出結果（表示能夠實現）。

## 一個好的演算法

一個演算法如果要設計好，必須達到以下目標：
- 正確性（correctness）：不含語法錯誤，對各種需求的輸入數據都能滿足。
- 可讀性（readability）：簡潔易懂。
- 健壯性（robustness）：非法數據被輸入時，不會引起演算法崩潰、出錯。
- 效率與低存儲量需求：即，演算法執行時間短 或 演算法記憶體使用量低。

## 總結

這週就簡敘述何謂演算法，下週將說明如何分析演算法的好壞。


## 目錄

前言：
- [何謂演算法？](/posts/200404_algorithm)
- [演算法分析](/posts/200406_analysis_of_algorithms)

資料結構：
- [資料結構概述](/posts/200419_data_structure)
- [陣列 Array](/posts/200426_ds_array)
- [鏈結串列 Linked list](/posts/200427_ds_linkedlist)
- [堆疊 ＆ 佇列](/posts/200504_ds_stack_queue)
- [集合 & 映射](/posts/200517_ds_set_map)
- [樹 & 二元樹](/posts/2006/ds_tree_and_binary_tree/)
- [二元搜尋樹](/posts/2006/ds_binary_search_tree/)
- [AVL-Tree](/posts/2007/ds_avl-tree/)
- [紅黑樹（上）：B 樹、2-3-4 樹](/posts/2007/ds_redblack-tree-1)
- [紅黑樹（中）：新增操作](/posts/2007/ds_redblack-tree-2)
- [紅黑樹（下）：刪除操作](/posts/2007/ds_redblack-tree-3)
- [樹堆](/posts/2008/ds_treap/)

排序演算法：
- [排序演算法](/posts/200518_sort_algorithm)
- [合併排序](/posts/200525_sort_algorithm_merge_sort)
- [快速排序](/posts/2000601_sort_algorithm_quick_sort)
- [希爾排序](/posts/2006/sort_algorithm_shell_sort/)
- [堆積排序](/posts/2006/sort_algorithm_heap_sort/)

搜尋演算法：
- [搜尋演算法](/posts/2008/search_algorithm/)

洗牌演算法：
- [洗牌演算法](/posts/2008/shuffle_algorithm/)

演算法設計：
- [遞迴](/posts/2008/alg_recursion/)
- [分治法、動態規劃](/posts/2009/divide_and_conquer_dpn/)