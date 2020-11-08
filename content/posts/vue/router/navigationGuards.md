---
title: "Vue Router - 導航守衛"
date: 2020-11-04T05:00:00+08:00
description: "導航守衛。"
keywords: ["Vue","Vue Router","竹白"]
tags: ["Vue", "Vue Router"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/router/navigationGuards.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## 什麼是導航守衛

**導航** 指的是路由的改變。路由路徑是能任意輸入的，當我們需要判斷權限或登錄狀態來決定使用者使否能訪問路由時，就會用到 **導航守衛（Navigation Guards）**，在路由發生變化時，做出相對應的處理。

簡單來說，就是在路由改變的過程中，會有一些函式鉤子可以讓我們用來處理一些事情。

## 完整的導航解析流程

先來看官方提供的導航解析流程：
1. 導航被觸發。
2. 在失活的元件裡呼叫 `beforeRouteLeave` 守衛。
3. 呼叫全域的 `beforeEach` 守衛。
4. 在重用的元件裡呼叫 `beforeRouteUpdate` 守衛（2.2+）。
5. 在路由配置裡呼叫 `beforeEnter`。
6. 解析非同步路由元件。
7. 在被激活的元件裡呼叫 `beforeRouteEnter`。
8. 呼叫全域的 `beforeResolve` 守衛（2.5+）。
9. 導航被確認。
10. 呼叫全域的 `afterEach` 鉤子。
11. 觸發 DOM 更新。
12. 呼叫 `beforeRouteEnter` 守衛中傳給 `next` 的回呼函式，創建好的元件實例會作為回呼函式的參數傳入。

導航守衛可以分成三種類型：
1. 全域守衛
2. 路由獨享守衛
3. 元件內守衛

## 全域守衛

全域守衛會監測所有路由，按順序分為：
- `beforeEach`
- `beforeResolve`
- `afterEach`

### 1. 前置守衛

全域前置守衛會在跳轉前觸發，常用於登入驗證。

使用 `router` 物件的 `beforeEach` 方法註冊：
```javascript
// router.js

const router = new VueRouter({ ... });

router.beforeEach((to, from, next) => {
  // ...
});
```

回呼函式接收的三個參數分別為：
- `to`：即將要進入的目標路由物件。
- `from`：當前導航正要離開的路由物件。
- `next`：為一個函式，用來交出控制權或者中斷導航。

`next` 函式會根據傳入的參數而有不同的行為：
- `next()`：不傳入參數，會進入下一個守衛。如果全部鉤子都執行完，導航狀態就是 confirmed（確認的）。
- `next(false)`：中斷當前導航。
- `next('/')` 或 `next({ path: '/' })`：跳轉至指定路由。
- `next(error)`：如果傳入一個 Error 實體，導航會被終止且該錯誤會被傳遞給 [`router.onError()`](https://router.vuejs.org/zh/api/#router-onerror)。

:::warning
`next` 函式一定要呼叫，因為守衛式非同步解析執行，此時導航在所有守衛 resolve 完之前一直處於 **等待中**。但要注意，只能呼叫一次。
:::

### 2. 解析守衛

在 2.5.0 之後的版本，可以用 `router.beforeResolve` 註冊一個解析守衛
```javascript
router.beforeResolve((to, from, next) => {
  // ...
});
```
用法基本上與 `beforeEach` 相同，區別在於被呼叫的時間點：一樣是在跳轉前觸發，但會在 `beforeEnter` 呼叫之後，也就是 **所有元件內守衛和非同步路由元件被解析之後** 才呼叫。


### 3. 後置鉤子

`afterEach` 會在導航被確認後觸發，所以稱為 **後置鉤子** 而不是守衛。

```javascript
router.afterEach((to, from) => {
  // ...
});
```
它沒有 `next` 參數可用，因此不會改變導航本身。

## 路由獨享的守衛

如果你不想全域設置守衛，可以單獨在指定路由上設置守衛。

定義 `beforeEnter` 守衛：
```javascript
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // ...
      },
    },
  ],
});
```
參數與 `beforeEach` 相同。


## 元件內的守衛

在元件內也可直接定義以下路由導航守衛：
- `beforeRouteEnter`
- `beforeRouteUpdate`
- `beforeRouteLeave`

```javascript
const Foo = {
  template: `...`,
  beforeRouteEnter (to, from, next) {
    // ...
  },
  beforeRouteUpdate (to, from, next) {
    // ...
  },
  beforeRouteLeave (to, from, next) {
    // ...
  }
}
```

### 1. beforeRouteEnter

`beforeRouteEnter` 守衛內 **不能** 使用 `this` 獲取元件實體，因為它是在導航 confirm（確認）前被呼叫，新元件還尚未建立。

不過，我們可以透過傳遞一個回呼函式給 `next`  來訪問元件實體：
```javascript
beforeRouteEnter (to, from, next) {
  next(vm => {
    // 透過 vm 訪問元件實體
  })
}
```

:::warning
`beforeRouteEnter` 是唯一支持 `next` 傳遞回呼函式的守衛。對於 `beforeRouteUpdate` 和 ` beforeRouteLeave` 來說，已經透過 `this` 取得元件實體，所以沒必要。
:::


### 2. beforeRouteUpdate

`beforeRouteUpdate` 會在當前路由改變，但是該元件被覆用時呼叫。

舉例來說，對於一個帶有動態參數的路徑 `/foo/:id`，在 `/foo/1` 和 `/foo/2` 之間跳轉的時候，由於會渲染同樣的 Foo 元件，因此元件實體會被覆用。這個情況下的變化，就會觸發 Foo 元件的 `beforeRouteUpdate`。

通常用於父路由監聽子路由的變化。

### 3. beforeRouteLeave

`beforeRouteLeave` 會在導航離開該元件的對應路由時呼叫。

通常用於以下情況：假設當前路由為一個修改頁面，當使用者還未保存就突然離開，我們可以透過 `next(false)` 來取消。
```javascript
beforeRouteLeave (to, from, next) {
  if (this.isConfirmed) {
    next();
  } else {
    if (confirm('還未保存，確定要離開嗎')) {
      next();
    } else {
      next(false);
    }
  }
}
```