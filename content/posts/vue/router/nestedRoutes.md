---
title: "Vue Router - 巢狀路由"
date: 2020-11-02T04:00:00+08:00
description: "巢狀路由。"
keywords: ["Vue","Vue Router","竹白"]
tags: ["Vue", "Vue Router"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/router/nestedRoutes.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## 巢狀路由

多層巢狀範例：
```
/user/foo/profile                     /user/foo/posts
+------------------+                  +-----------------+
| User             |                  | User            |
| +--------------+ |                  | +-------------+ |
| | Profile      | |  +------------>  | | Posts       | |
| |              | |                  | |             | |
| +--------------+ |                  | +-------------+ |
+------------------+                  +-----------------+
```

在巢狀的出口中渲染元件，需要在路由配置中使用 `children` 屬性，接受一個 RouteConfig 陣列。：
```javascript
{
  path: '/user/:id',
  component: User,
  children: [
    {
      path: 'profile',
      component: UserpProfile,
    },
    {
      path: 'posts',
      component: UserPosts,
    },
  ],
}
```

注意，巢狀時 `path` 不帶 `/`，否則會被當作根路徑。

子路由會被渲染在元件中的 `<router-view>` 位置：
```javascript
const User = {
  template: `
  <div>
    <h1>User</h1>
    <router-view />
  </div>
`,
};
```
如果希望 `/user/foo` 有預設子路由，可以提供一個空路徑：
```javascript
children: [
  { 
    path: '',
    component: UserHome
  },
  // ...
],
```

或者，你希望預設是子路由中的其中一個，可以使用 `redirect` 轉向：
```javascript
{
  path: '/user/:id',
  component: User,
  redirect: '/user/:id/profile',
  children: [
    {
      path: 'profile',
      component: UserProfile,
    },
    {
      path: 'posts',
      component: UserPosts,
    },
  ],
}
```

**CodePen Demo：**[Vue 2.x - 巢狀路由](https://codepen.io/CHUPAIWANG/pen/eYZqMLG)