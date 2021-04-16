---
title: "自定義元素"
date: 2021-03-14
description: "Vue2.x 與 Vue3.x 差異 - 自定義元素"
keywords: ["Vue3", "Vue2.x 與 Vue3.x 差異"]
tags: ["Vue3", "Vue2.x 與 Vue3.x 差異"]
series: ["Vue3 筆記"]
image: images/covers/2103/customElements.png
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

## 自定義元素

若想要添加在 Vue 外部定義的自定義元素（例如 [Web Components API](https://developer.mozilla.org/zh-CN/docs/Web/Web_Components)），需要將其設為白名單，否則會拋出警告。

以下將以 `<plastic-button></plastic-button>` 模板為例。

### 1. 2.x 語法

在 Vue 2.x 中，自定義元素白名單是透過 [`Vue.config.ignoredElements`](https://cn.vuejs.org/v2/api/#ignoredElements) 設置：
```javascript
// Vue 2.x
Vue.config.ignoredElements = ['plastic-button'];
```

### 2. 3.x 語法

在 Vue 3.x 自定義元素白名單現在在模板編譯期間執行，因此改透過 `vue-loader` 的 `compilerOptions` 選項傳遞。

webpack 中設置：
```javascript
// in webpack config
rules: [
  {
    test: /\.vue$/,
    use: 'vue-loader',
    options: {
      compilerOptions: {
        isCustomElement: tag => tag === 'plastic-button'
      }
    }
  }
  // ...
]
```

使用動態模板編譯，透過 `app.config.isCustomElement` 傳遞：
```javascript
// Vue 3.x
const app = Vue.createApp({});
app.config.isCustomElement = tag => tag === 'plastic-button';
```

## 使用 is 定義

另一種將自定義元素用作自定義內置模板的方法是向內置元素添加 `is` attribute：
```html
<!-- Vue 3.x -->
<button is="plastic-button">點擊我!</button>
```

`is` 的行為 Vue 2.x 與 Vue 3.x 並不相同，原本的行為到了 Vue 3.x 只保留對於 `<component>` tag 上使用，其餘元素要透過 `v-is` 指令。

## v-is 指令

>本節僅影響直接在頁面的 HTML 中寫入 Vue 模板的情況。詳細問題情況可以參考 **[Vue2.x - 解析 DOM 模板時的注意事項](https://cn.vuejs.org/v2/guide/components.html#%E8%A7%A3%E6%9E%90-DOM-%E6%A8%A1%E6%9D%BF%E6%97%B6%E7%9A%84%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9)**。

### 1. 2.x 語法

在 Vue 2.x 中，可以使用使用 `is` prop 來解決：
```html
<!-- Vue 2.x -->
<table>
  <tr is="blog-post-row"></tr>
</table>
```

### 2. 3.x 語法

隨著 [`is`](https://v3.cn.vuejs.org/api/special-attributes.html#is) attribute 的行為變化，Vue 3.x 引入了一個新的指令 `v-is`，用於解決這些情況：
```html
<!-- Vue 3.x -->
<table>
  <tr v-is="'blog-post-row'"></tr>
</table>
```
{{< notice warning>}} 
`v-is` 類似於 Vue 2.x 的 `:is` 動態綁定，因此元件名稱記得使用字串。
{{< /notice >}}
