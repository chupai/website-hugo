---
title: "移除 API"
date: 2021-03-16
description: "Vue2.x 與 Vue3.x 差異 - 移除 API"
keywords: ["Vue3", "Vue2.x 與 Vue3.x 差異"]
tags: ["Vue3", "Vue2.x 與 Vue3.x 差異"]
series: ["Vue3 筆記"]
image: images/covers/2103/removedAPI.png
draft: false
---


>本篇為官方文件 **[Migration from Vue 2](https://v3.vuejs.org/guide/migration/introduction.html)** 筆記。
- [全域 API](/posts/2103/globalapi/)
- [模板指令](/posts/2103/templatedirectives/)
- [元件 & 渲染函數](/posts/2103/componentsrenderfunction/)
- [自定義元素](/posts/2103/customelements/)
- [其他小改變](/posts/2103/other)
- [移除 API](/posts/2103/removedapi/)

<!--more-->

{{< featuredImage >}}

## 按鍵修飾符號

- 不再支持使用數字（`keyCode`）作為 `v-on` 修飾符號
- 不再支持 `config.keyCodes`

### 1. 2.x 語法

在 Vue 2.x 可以使用鍵盤事件的 [`keyCode`](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/keyCode) 作為修飾符號：
```html
<!-- Vue 2.x -->
<input v-on:keyup.13="submit">
```

另外可透過全域 [`config.keyCodes`](https://cn.vuejs.org/v2/api/#keyCodes) 物件，自定義按鍵修飾符號別名。
```javascript
// Vue 2.x 
Vue.config.keyCodes = {
  f1: 112
}
```

### 2. 3.x 更新

本來就不建議繼續使用鍵盤事件的 [`keyCode`](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/keyCode) 屬性，因為該用法已經被廢棄，雖然目前因為舊程式碼仍保留支援，但為了未來瀏覽器，盡可能不使用它。

因此 Vue 在 3.x 版本移除了對它支持，包括 `config.keyCodes` 也已棄用。

與 Vue 2.x 相同，使用鍵盤事件的 [`key`](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key) 屬性作為飾符號即可：
```html
<!-- Vue 3.x -->
<input v-on:keyup.delete="confirmDelete" />

<!-- 若遇到 PageDown 這類名稱，轉成 kebab-case -->
<input v-on:keyup.page-down="onPageDown">
```

## 事件 API

[Vue 2.x 實體方法事件 API](https://cn.vuejs.org/v2/api/#实例方法-事件) 中的 `$on`、`$off` 和 `$once` 已被移除，只剩 `$emit` 到保留。

在 Vue 3.x 須改用使用實現了事件觸發 API 的第三方函式庫來取代現有的 event hub，例如 **[mitt](https://github.com/developit/mitt)** 或 **[tiny-emitter](https://github.com/scottcorgan/tiny-emitter)**。


## 過濾器

從 Vue 3.0 開始，[過濾器（Filter）](https://cn.vuejs.org/v2/guide/filters.html)已刪除，建議改用 method 或 computed 替代。


## 內聯模板 Attribute

Vue 3.x 將不再支持 **[內聯模板](https://cn.vuejs.org/v2/guide/components-edge-cases.htmll#内联模板)** 功能。我個人是沒用過，詳細說明請參考 **[官方文件](https://v3.cn.vuejs.org/guide/migration/inline-template-attribute.html#内联模板-attribute)**。

不過，`inline-templat` 會讓模板的作用域變得更加難以理解，因此在 Vue 2.x 本來就不是最優先的選擇。


## \$children

[`$children`](https://cn.vuejs.org/v2/api/#vm-children) 實體屬性可以用來訪問當前所有子元件實體，但已從 Vue 3.0 中移除，可以使用 [`$refs`](https://v3.cn.vuejs.org/guide/component-template-refs.html#模板引用) 替代。

