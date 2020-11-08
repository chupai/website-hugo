---
title: "Vue Router - 程式控制"
date: 2020-11-02T05:00:00+08:00
description: "程式控制。"
keywords: ["Vue","Vue Router","竹白"]
tags: ["Vue", "Vue Router"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/router/programmaticNavigation.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## push 方法

除了使用 `<router-link>` 創建 `a` 標籤來定義導航連結，還可以使用路由的實體方法來實作。

```javascript
router.push(location, onComplete?, onAbort?);
```

當你點擊 `<router-link>` 時，`push` 方法會在內部呼叫，所以 `<router-link :to="...">` 等同於呼叫 `router.push(...)`。

`location` 參數可以是字串或物件：
```javascript
// 字串
router.push('home');

// 物件
router.push({ path: 'home' });

// 命名的路由
router.push({ name: 'user', params: { userId: '123' } });

// 帶查詢參數，變成 /register?plan=private
router.push({ path: 'register', query: { plan: 'private' } });
```

如果提供了 `path`，`params` 會被忽略。建議改用以下作法：
```javascript
// -> /user/123

const userId = '123';
router.push({ name: 'user', params: { userId } });
router.push({ path: `/user/${userId}` });
```
同樣的規則也適用於 `<router-link>` 元件的 `to` 特性。

更多關於 **[命名的路由](/posts/vue/router/named/)** 之後會說明。

### 1. 可選參數

另外兩個可選參數為回呼函式：
- `onComplete`：導航成功完成（在所有的異步鉤子被解析之後）時進行呼叫。
- `onAbort`：導航終止（導航到相同的路由、或在當前導航完成之前導航到另一個不同的路由）時進行呼叫。

在 3.1.0+ 之後，如果省略兩個可選參數，會回傳 Promise 物件，可以使用以下作法：
```javascript
router.push(location).then(onComplete).catch(onAbort);
```

## replace 方法

`replace` 方法基本上與 `push` 方法 相同，唯一差異在於它不會向 history 添加新記錄。

等同 `<router-link>` 設置 [`replace`](https://router.vuejs.org/zh/api/#replace) 特性。


## go 方法

`go` 方法的參數為整數，意思是在 history 記錄中向前或者後退多少步。

```javascript
router.go(n);
```

舉例來說：
```javascript
// 在瀏覽器記錄中前進一步，等同於 history.forward()
router.go(1);

// 後退一步記錄，等同於 history.back()
router.go(-1);

// 前進 3 步記錄
router.go(3);

// 如果 history 記錄不夠用，默默地失敗
router.go(-100);
router.go(100);
```


## 導航失敗

```html
<router-link to="/about">About</router-link>
<button @click="$router.push('/about')">About</button>
```

這裡要注意，若我們當前路徑與目標路徑相同，使用 `<router-link>` 的並不會拋出錯誤，而使用程式控制的會拋出錯誤。

**CodePen Demo**：[Vue 2.x - 導航失敗](https://codepen.io/CHUPAIWANG/pen/BazrQmJ)

要如何處理，可以參考官方文件說明 **[Navigation Failures](https://router.vuejs.org/guide/advanced/navigation-failures.html#navigation-failures)**。