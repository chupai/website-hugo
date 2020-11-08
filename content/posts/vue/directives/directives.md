---
title: "Vue 指令概述"
date: 2020-10-12
description: "Vue 指令概述。"
keywords: ["Vue","Vue指令","竹白"]
tags: ["Vue", "Vue指令"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/directives/directives.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## 指令（Directives）

在 Vue 中有提供一些帶有前綴 `v-` 的指令（Directives），主要用於 HTML 元素標籤中。每個指令都會有一個 **預期值**，指的是想綁定的值，為 JavaScript 表達式（`v-for` 是例外情況），我們可以直接在 HTML 上去綁定我們需要做的事情。
 
## 參數（Arguments）

一些指令能夠接收一個「參數」，在指令名稱之後以冒號表示，例如 `v-bind`、`v-on` 等：

- `v-bind` 接收的參數為 HTML 特性
- `v-on` 接收的參數為 DOM 事件

```html
<a v-bind:href="url">...</a>

<a v-on:click="doSomething">...</a>
```

## 動態參數（Dynamic Arguments）

如果我們想在 `v-bind` or `v-on` 中使用動態參數，在 2.6.0 版之前，只能使用物件的語法：

```html
<a v-bind="{ [attributeName]: url }"> ... </a>

<a v-on="{ [eventName]: doSomething }"> ... </a>
```

但這種做法有幾種缺點：
- `vue-template-compier` 生成了低效的程式碼
- `v-slot` 沒有類似的使用物件的語法

Vue 在 2.6.0 版後，新增了動態參數的新語法，可以使用方括號 `[]` 將參數用表達式表示：
```html
<a v-bind:[attributeName]="url"> ... </a>

<a v-on:[eventName]="doSomething"> ... </a>
```

動態參數表達式有一些限制，因為某些字元，如空格和引號，放在 HTML attribute 名裡是無效的。

## 修飾符號（Modifiers）

修飾符號（modifier）是以 `.` 指名的特殊後綴，用於指出一個指令應該以特殊方式綁定。

舉例來說，`.prevent` 修飾符號，可對 `v-on` 觸發的事件函式呼叫 `event.preventDefault()`：
```html
<form v-on:submit.prevent="onSubmit">...</form>
```

每個指令都有自己的修飾符號，會在之後詳細說明。

## 縮寫

`v-bind` 和 `v-on` 這兩個最常用的指令，Vue 提供了縮寫。

```html
<!-- 完整語法 -->
<a v-bind:href="url">...</a>

<!-- 縮寫 -->
<a :href="url">...</a>
```
```html
<!-- 完整語法 -->
<a v-on:click="doSomething">...</a>

<!-- 縮寫 -->
<a @click="doSomething">...</a>
```

{{< notice warning >}} 
在某些情況下，可能不需要使用參數，但是要注意，沒參數無法使用縮寫，只能使用完整語法。
{{< /notice >}}
