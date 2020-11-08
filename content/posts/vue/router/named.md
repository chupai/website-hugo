---
title: "Vue Router - 命名路由 & 命名視圖"
date: 2020-11-03T04:00:00+08:00
description: "程式控制。"
keywords: ["Vue","Vue Router","竹白"]
tags: ["Vue", "Vue Router"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/router/named.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## 命名路由

定義路由還可以使用 `name` 屬性來設置名稱：
```javascript
routes: [
  {
    path: '/user/:id',
    name: 'user',
    component: User
  }
]
```

`to` 特性使用 `v-bind` 來綁定一個物件傳入路由名稱和動態路由參數：
```html
<router-link :to="{ name: 'user', params: { id: 'foo' } }">User</router-link>
```
當路徑比較複雜的時候，使用命名路由代替，會是一個選擇。但要注意，`path` 和 `name` 不可以混用，`path` 會被無視。

**CodePen Demo：**[Vue 2.x - 命名路由](https://codepen.io/CHUPAIWANG/pen/JjKPbZR)


## 命名視圖

假設要同一個頁面載入兩個以上的視圖，就需要替 `<router-view>` 元件命名。

```html
<router-view></router-view>
<router-view name="a"></router-view>
<router-view name="b"></router-view>
```

多個視圖就需要多個元件，因此路由設置需要改用 `components`：
```javascript
routes: [
  {
    path: '/',
    components: {
      default: Foo,
      a: Bar,
      b: Baz,
    }
  },
]
```
`<router-view>` 沒命名預設為 `default`，如果具名會渲染相對設定元件。


**CodePen Demo：**[Vue 2.x - 命名視圖](https://codepen.io/CHUPAIWANG/pen/OJXWYwO)