---
title: "Vue Router - 滾動行為"
date: 2020-11-06
description: "滾動行為。"
keywords: ["Vue","Vue Router","竹白"]
tags: ["Vue", "Vue Router"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/router/scroll.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

{{< notice warning >}} 
這個功能只在支持 `history.pushState` 的瀏覽器中可用。
{{< /notice >}}

```javascript
const router = new VueRouter({
  routes: [...],
  scrollBehavior(to, from, savedPosition) {
    // return {}
  },
});
```
`scrollBehavio` 方法接收參數為： 
- `to`：即將要進入的目標路由物件。
- `from`：當前導航正要離開的路由物件。
- `savedPosition`：這個參數只有當 `popstate` 導航（透過瀏覽器的前進、後退按鈕觸發）時才可用。

這個方法需要回傳一個滾動位置物件，有兩種格式：
- `{ x: number, y: number }`
- `{ selector: string, offset? : { x: number, y: number }}`（`offset` 只在 2.6.0+ 支持）

如果回傳一個 `falsy` 值或空物件，則不會發生滾動。

對於所有路由導航，簡單地讓頁面滾動到頂部：
```javascript
scrollBehavior(to, from, savedPosition) {
  return { x: 0, y: 0};
}
```

若是透過瀏覽器的前進、後退按鈕切換路由時，可以使用 `savedPosition` 取得上次位置：
```javascript
scrollBehavior(to, from, savedPosition) {
  if (savedPosition) {
    return savedPosition;
  } else {
    return { x: 0, y: 0};
  }
}
```

若希望路由切換時，讓頁面滾動到指定位置，可以利用 `hash`，模擬「滾動到錨點」的行為：
```htmlmixed
<router-link :to="{name: 'nav', hash: '#nav'}">nav</router-link>
```
```javascript
scrollBehavior(to, from, savedPosition) {
  if (to.hash) {
    return { selector: to.hash };
  }
}
```

若是希望滾動位置可以預留空間，可以加上 `offset` 屬性：
```javascript
scrollBehavior(to, from, savedPosition) {
  if (to.hash) {
    return { 
      selector: to.hash,
      offset: { x: 0, y: 100 },
    };
  }
}
```


## 非同步滾動

2.8.0 新增了非同步滾動，可以回傳一個 Promise 來處理
```javascript
  scrollBehavior(to, from, savedPosition) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve({ x: 0, y: 0 });
      }, 500);
    });
  },
```
