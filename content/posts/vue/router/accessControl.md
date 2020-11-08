---
title: "Vue Router - 權限控制"
date: 2020-11-05
description: "權限控制。"
keywords: ["Vue","Vue Router","竹白"]
tags: ["Vue", "Vue Router"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/router/accessControl.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## Meta Fields

如果你的頁面需要通過會員登入驗證才能訪問，或者是不同角色（例如：訪客、會員、管理員）的權限判斷，定義路由時，可以設置一個 `meta` 選項，用來儲存一些識別用資料。

舉例來說，路由 `/user` 需要通過驗證才能訪問。

如果直接在 `beforeEach` 全域前置守衛中用路徑判斷：
```javascript
beforeEach((to, from, next)=> {
  if (to.path="/user" && !isAuthenticated) {
    next({ name: 'Login' });
  } else {
    next();
  }
});
```
這樣會遇到一些問題：
1. `/user` 下的子路由無法檢查；
2. 其它路由也需要通過驗證時，判斷寫起來很麻煩。

我們可以在 `/user` 路由定義一個 `meta`：
```javascript
const router = new VueRouter({
  routes: [
    {
      path: '/',
      component: Home,
    },
    {
      path: '/login',
      name: 'Login'
      component: Login,
    },
    {
      path: '/user',
      component: User,
      meta: { 
        requiresAuth: true,
      }
    }
  ]
});
```

這樣我們就只需要訪問路由物件的 `meta` 是否有我們定義屬性，用它來判斷是否需要驗證：
```javascript
beforeEach((to, from, next)=> {
  if (to.meta.requiresAuth && !isAuthenticated) {
    next({ name: 'Login' });
  } else {
    next();
  }
});
```
{{< notice success >}} 
`isAuthenticated` 為驗證狀態，通常會透過 Vuex 全域管理控制。使用者輸入帳號密碼通過後端伺服器驗證之後，後端會回傳一個 token，前端可以透過 localStorage 或 cookie 將它緩存，接下來只需要驗證是否存在、過期、正確性。
{{< /notice >}}

如果有子路由，你又不想為每個子路由都重複設置 `meta`，可以使用 `$route.matched` 路由紀錄來判斷：
```javascript
router.beforeEach((to, from, next) => {
  if (to.matched.some((record) => record.meta.requiresAuth) && !isAuthenticated) {
    next({ name: 'Login' });
  } else {
    next();
  }
});
```
`matched` 為陣列，裡面包含匹配成功的路由紀錄，因此使用 `some()` 遍歷檢查是否需要驗證。

**CodePen Demo**：[Vue 2.x - Meta Fields 簡易範例](https://codepen.io/CHUPAIWANG/pen/LYZQaXr)

## AddRoutes

上面的作法是前端寫好所有路由，後端在回傳當前使用者的權限，根據預先定義好的路由規則進行驗證。

權限控制的另一種作法就是，一開始前端規劃的路由都是不需要驗證的，而需要驗證路由的則是從後端取得後，以動態添加的方式加載到路由實體中。

[`addRoutes`](https://router.vuejs.org/zh/api/#router-addroutes) 動態添加更多的路由規則：
```javascript
router.addRoutes(routes: Array<RouteConfig>)
```
參數必須是一個符合 `routes` 選項要求的陣列。