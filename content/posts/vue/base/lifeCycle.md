---
title: "Vue 生命週期"
date: 2020-10-10
description: "生命週期簡單來說就是「從 Vue 實體建立到銷毀的過程」。"
keywords: ["Vue","Vue生命週期","竹白"]
tags: ["Vue", "Vue生命週期"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/base/lifeCycle.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

Vue 的實體（Instance）是 Vue.js 的核心，每個 Vue App 都是從建立實體開始。

<!--more-->
{{< featuredImage >}}

## 生命週期（Life Cycle）

生命週期簡單來說就是「從 Vue 實體建立到銷毀的過程」。

可以分為 4 個階段：
1. 初始化
2. 模板編譯
3. 掛載
4. 銷毀

在這些過程中，還會呼叫 **[生命週期鉤子（Hook）](https://cn.vuejs.org/v2/api/index.html#选项-生命周期钩子)**，我們可以在這些鉤子上做額外的處理。

生命週期鉤子的觸發時間點：
- `beforeCreate`：實體剛被建立，資料觀測和事件綁定都尚未初始化。
- `created`：實體已建立，資料觀測和事件綁定初始化完成。
- `beforeMount`：已經完成了模板的編譯，但尚未掛載。
- `mounted`：實體掛載完成，`el` 的目標 DOM 被 `$el` 所替換。
- `beforeUpdate`：資料更新時，重新渲染 DOM 之前。
- `updated`：DOM 重新渲染後。
- `beforeDestroy`：實體銷毀前，此時實體依然可以使用。
- `destroyed`：實體銷毀後。

## 初始化階段

![](https://i.imgur.com/gVbCRU3.png)

`new Vue()` 到 `created` 鉤子之間的階段叫做初始化階段，主要目的為初始化一些屬性、事件以及響應式資料等等。

### 1. beforeCreate 鉤子

`beforeCreate` 鉤子是 `new Vue()` 之後觸發的第一個鉤子：
- 這個階段元素尚未被掛載；
- 資料尚未被建立，`data`、`computed`、`methods`、`watch` 等等皆無法取得。

### 2. created 鉤子

`created` 鉤子，Vue 實體已建立完成：
- 這個階段元素一樣尚未被掛載；
- `data`、`computed`、 `methods`、`watch` 等等已可取得。


資料操作至少要等到 `created` 階段，因此通常我們用 AJAX 取得資料操作會在這裡執行，但不建議過多的請求，避免網頁無畫面的時間過長，可以考慮在 `mounted` 階段做出讀取畫面再打操作。

## 模板編譯階段

![](https://i.imgur.com/RM4XRNJ.png)

`created` 鉤子到 `beforeMount` 鉤子之間的階段叫做模板編譯階段，主要目的為將模板編譯為 `render` 函式。

會先判斷是否有指定 `el` 選項：
- 有，繼續往下執行。
- 沒有，會停止生命週期，直到呼叫 `vm.$mount(el)` 才會進行下一步。

接下來繼續判斷是否指定 `template` 選項：
- 有，就直接將模板編譯為 `render` 函式。
- 沒有，則使用 `el` 進行編譯。

### 1. beforeMount 鉤子

`beforeMount` 鉤子發生在執行掛載之前，虛擬 DOM 已經建立完成，即將開始渲染。

## 掛載階段

![](https://i.imgur.com/mur0uvO.png)

`beforeMount` 鉤子到 `mounted` 鉤子之間為掛載階段，這個階段 Vue 會將虛擬 DOM 掛載到頁面上。

掛載後，Vue 會持續追蹤資料變化，當資料發生改變會重新渲染畫面。

### 1. mounted 鉤子

`mounted` 鉤子會在開始掛載時呼叫：
- 可以對 DOM 進行操作，例如使用 [`$refs`](https://cn.vuejs.org/v2/api/#vm-refs)；
- 但 `mounted` 不會保證所有的子元件也都一起被掛載，如果你希望等到全部渲染完畢，可以用 [`$nextTick`](https://cn.vuejs.org/v2/api/#vm-nextTick) 來確保。

### 2. beforeUpdate

`beforeUpdate` 鉤子會在資料發生改變時呼叫，資料雖然已經更新，但畫面還是舊資料：
- 適合在更新之前訪問現有的 DOM，手動移除已添加的事件監聽器。


### 3. updated

`updated` 鉤子會在畫面更新後呼叫：
- 可以執行依賴於 DOM 的操作，但跟 `mounted` 一樣，不保證所有的子元件都更新完畢，可使用 [`$nextTick`](https://cn.vuejs.org/v2/api/#vm-nextTick) 來確保；
- 避免在此更改資料狀態，可能會造成死循環，應該使用 `watch` 屬性來處理。

## 銷毀階段

![](https://i.imgur.com/8WDHkkV.png)

Vue 在呼叫 `$destroy()` 後，生命週期就會進入銷毀階段，將解除實體的所有綁定、所有事件監聽移除、子元件實體也會被銷毀。

### 1. beforeDestroy 

`beforeDestroy` 鉤子會在銷毀之前呼叫：
- 實體仍然可用；
- 一般會在這做一些提示、重置操作。

### 2. destroyed

`destroyed` 鉤子會在銷毀後呼叫，此時已經無法操作實體了。


## keep-alive 專屬生命週期

- `activated`：元件激活時呼叫。
- `deactivated`：元件被銷毀時呼叫。