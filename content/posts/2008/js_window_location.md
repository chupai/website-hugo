---
title: "window.location"
date: 2020-08-19
description: "Location 物件是瀏覽器提供的原生物件，提供 URL 相關的資訊和操作方法。"
keywords: ["JavaScript", "window.location", "竹白"]
tags: ["JavaScript", "window.location"]
categories: ["JavaScript筆記"]
image: images/covers/200819_js_window_location.png
draft: false
---

>[JavaScript筆記 目錄](/posts/190620_javascript)

<!--more-->

{{< featuredImage >}}

## Location 物件

Location 物件是瀏覽器提供的原生物件，提供 URL 相關的資訊和操作方法。

可以透過 `window.location` 或 `document.location` 取得。

### 1. 屬性

- `Location.href`：網址（URL）
- `Location.protocol`：網址中的通訊協定，包括冒號 `:`
- `Location.host`：主機名稱
- `Location.hostname`：網域名稱
- `Location.port`：	網址中的埠號
- `Location.pathname`：網址路徑，從根路徑 `/` 開始
- `Location.search`：網址參數，從問號 `?` 開始
- `Location.hash`：網址 hash 值，從 `#` 開始
- `Location.username`：域名前面的使用者名稱
- `Location.password`：域名前面的密碼
- `Location.origin`：URL 的協議

只有 `origin` 屬性是唯讀。


### 2. 方法

- `Location.assign()`：接受一個 URL 字串作為參數，瀏覽器會立刻跳轉至新網址。
- `Location.replace()`：與 `assign()` 相同，差異在於 `replace()` 會刪除瀏覽器歷史 History 的當前網址，這表示回到上一頁的按鈕無法回到原先的網址。
  - 常應用在行動裝置版的網頁跳轉
- `Location.reload()`：重新載入當前網址，等同按下瀏覽器刷新按鈕。
- `Location.toString()`：回傳 URL 字串，等同讀取 `Location.href` 屬性。

## 網頁跳轉

如果對 `Location.href` 寫入完整的 URL，瀏覽器會立刻跳轉到新網頁上：
```javascript
window.location.href = 'https://www.google.com';

// 等同
window.location.assign('https://www.google.com');
```

直接改寫 `location`，也相當於寫入 `href` 屬性：
```javascript
window.location = 'https://www.google.com';
```

如果不是完整網址，可用於切換路徑、滾動到新的錨點等等：
```javascript
window.location.href = '/posts';
// 等同
window.location.pathname = '/post'

window.location.href = '#top';
// 等同
window.location.hash = '#top';
```