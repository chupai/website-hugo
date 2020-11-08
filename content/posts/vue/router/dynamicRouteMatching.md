---
title: "Vue Router - 動態路由匹配"
date: 2020-11-01T05:00:00+08:00
description: "動態路由是指在 URL 路徑中含有動態參數的路由。"
keywords: ["Vue","Vue Router","竹白"]
tags: ["Vue", "Vue Router"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/router/dynamicRouteMatching.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## 動態路由

動態路由是指在 URL 路徑中含有動態參數的路由。例如有一個 `User` 元件，對於所有 ID 各不相同的用戶，都要使用這個元件來渲染。那麼路徑就會是 `/user/1`、`/user/2`、`/user/3` 等等。

### 1. 路徑參數 params

一個 **路徑參數** 使用冒號 `:` 標記：
```javascript
routes: [
  { 
    path: '/user/:id',  // 動態路徑參數 以冒號開頭
    component: User
  },
]
```
這樣，任何匹配 `/user/:id` 的路徑都會渲染出 `User` 元件。

路由物件 [`this.$route`](https://router.vuejs.org/zh/api/#%E8%B7%AF%E7%94%B1%E5%AF%B9%E8%B1%A1) 中的 `params` 屬性表示當前動態路徑參數值。

**CodePen Demo：**[Vue 2.x - 動態路由 路徑參數](https://codepen.io/CHUPAIWANG/pen/QWNXeYV)

### 2. 多段路徑參數

你可以在一個路由中設置多段 **路徑參數**，對應的值都會設置到 `params` 屬性中。例如：

```
模式：
/user/:id
/user/:username/post/:post_id

匹配路徑：
/user/evan
/user/evan/post/123

params 物件：
{ username: 'evan' }
{ username: 'evan', post_id: '123' }
```

## 響應路由參數的變化

當使用路由參數時，元件會重複利用，這意味著元件的生命週期鉤子不會再被呼叫。

因此，若元件想對路由參數的變化作出響應的話，可以使用 `watch` 監聽：
```javascript
const User = {
  template: '...',
  watch: {
    $route(to, from) {
      // 對路由變化作出響應...
    },
  },
};
```

或者使用 `beforeRouteUpdate()` [導航守衛](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html#导航守卫)：

```javascript
const User = {
  template: '...',
  beforeRouteUpdate(to, from, next) {
    // react to route changes...
    // don't forget to call next()
  },
};
```

就算是分開定義路由，如果使用相同元件，一樣會重複使用元件，例如：
```javascript
{ 
  path: '/user/1',
  component: User
},
{ 
  path: '/user/2',
  component: User
}
```

## 捕獲所有路由或 404 Not found 路由

通用符號 `*` 可以捕獲所有路由路徑，通常用於例外處理。

當路徑不存在時，可以渲染一個錯誤警告，告知使用者當前網址不存在：
```javascript
{
  path: '*',
  component: Error,
}
```

但是這樣子不存在的路徑會保留，因此你可以使用 [`redirect`](https://router.vuejs.org/zh/guide/essentials/redirect-and-alias.html#%E9%87%8D%E5%AE%9A%E5%90%91%E5%92%8C%E5%88%AB%E5%90%8D) 轉向 `/404`：
```javascript
{
  path: '/404',
  component: Error,
},
{
  path: '*',
  redirect: '/404',
}
```

當使用通用符號路由時，請確保路由的順序是正確的，也就是說含有通用符號的路由應該放在最後。

### 1. pathMatch 參數

使用一個通用符號時，`$route.params` 內會自動添加一個名為 `pathMatch` 參數。包含了 URL 通過通用符號被匹配的部分。

```javascript
//  { path: '/user-*' }
this.$router.push('/user-admin');
this.$route.params.pathMatch; // 'admin'

//  { path: '*' }
this.$router.push('/non-existing');
this.$route.params.pathMatch; // '/non-existing'
```

## 高級匹配模式

Vue Router 使用 **[path-to-regexp](https://github.com/pillarjs/path-to-regexp/tree/v1.7.0)** 作為路徑匹配引擎，所以可以使用 **正則表達式** 支持很多高級的匹配模式。

**[官方文件範例](https://github.com/vuejs/vue-router/blob/dev/examples/route-matching/app.js)**

### 1. 可選路由參數

`?` 等同於 `{0,1}` 表示零個或一個，也就是可選。

無論是否添加路由參數都對應的同一個元件：
```javascript
{ path: '/optional-params/:foo?' }
```
```html
<router-link to="/optional-params"></router-link>
<router-link to="/optional-params/foo"></router-link>
```

可以在元件內使用 `v-if` 搭配 `$route.params` 渲染不同的內容。

### 2. 精確匹配參數

使用正則規則驗證，只有符合的格式才會跳轉。

舉例來說，必須是正整數：
```javascript
{ path: '/params-with-regex/:id(\\d+)' }
```
```html
<router-link to="/params-with-regex/123"></router-link>
```
`\d` 等同`[0-9]` 匹配 0 到 9 之間的任一數字、`+` 等同於 `{1,}` 表示至一個以上。

純數字的驗證通常用於分頁。另外，精確匹配還可以用來驗證手機、信箱等等資料格式的參數，符合的才允許跳轉。

### 3. 匹配任意路徑

之前提過的 `*` 通用符號，匹配任意路徑。

```javascript
{ path: '/asterisk/*' }
```
```html
<router-link to="/asterisk/foo"></router-link>
<router-link to="/asterisk/foo/bar"></router-link>
```

### 4. 部分可選參數

結合可選路由參數和精確匹配參數：
```javascript
{ path: '/optional-group/(foo/)?bar' }
```
```html
<router-link to="/optional-group/bar"></router-link>
<router-link to="/optional-group/foo/bar"></router-link>
```

## 匹配優先級

有時候，同一個路徑可以匹配多個路由，此時，匹配的優先級就按照路由的定義順序：誰先定義的，誰的優先級就最高。

