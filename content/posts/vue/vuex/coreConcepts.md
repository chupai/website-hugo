---
title: "Vuex - 核心概念"
date: 2020-10-27
description: "Vuex 核心概念。"
keywords: ["Vue","Vuex","竹白"]
tags: ["Vue", "Vuex"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/vuex/coreConcepts.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## 核心概念

一般元件上，我們會用 `data` 儲存狀態資料、用 `method` 操作狀態資料，還會用 `computed` 衍生狀態資料。

而在 Vuex 則是透過以下選項來管理狀態資料：
1. State：儲存狀態資料，類似全域的 `data`。
2. Mutations：更新態的方法，只能同步操作。
3. Actions：進行非同步狀態更新操作，類似全域的 `method`。
4. Getters：衍生狀態資料，類似全域的 `computed`。

![](https://i.imgur.com/xwIpEDs.png)

## State

State 就是用來存放共享狀態資料的倉庫。

定義 [`state`](https://vuex.vuejs.org/zh/api/#state) ：
```javascript
// store.js

state: {
  count: 0,
}
```
與 `data` 一樣需要遵守 Vue 的響應規則。

### 1. 訪問狀態

在元件內，我們可以透過 `$store`（Store 實體）訪問到這個 `state` 物件。

```javascript
$store.state
```

通常會使用計算屬性回傳狀態：
```javascript
// Component.vue

computed: {
  count() {
    return this.$store.state.count;
  },
}
```

**CodePen Demo：**[Vue 2.x - Vuex State](https://codepen.io/CHUPAIWANG/pen/abZvBdY)

{{< notice warning >}} 
注意，[`state`](https://vuex.vuejs.org/zh/api/#state-2) 物件，只能透過 Mutations 更新。
{{< /notice >}}


### 2. 多個狀態

當一個元件需要獲取多個狀態時候，將這些狀態都宣告為計算屬性會有些重複和冗餘。因此我們可以使用 [`mapState`](https://vuex.vuejs.org/zh/api/#mapstate) 輔助函式 ，取得多個 `state` 裡面的狀態資料。

假設 `state` 物件內有多筆狀態資料：
```javascript
// store.js

state: {
  count: 0,
  total: 0,
}
```

首先我們必須在元件中引入 `mapState` 輔助函式：
```javascript
// Component.vue

import { mapState } from 'vuex';
```

`mapState` 輔助函式可以傳入物件或陣列。如果傳入一個物件有三種寫法：
1. 箭頭函式，可以使程式碼更簡潔。
2. 字串，等同使用箭頭函式。
3. 如果要使用 `this` 獲取區域狀態，需要改用一般的函式。
```javascript
// Component.vue

computed: mapState({
  // 箭頭函式
  count: state => state.count,
  // 字串
  total: 'total',
  // 一般的函式
  countPlusLocalState(state) {
    return state.count + this.localCount;
  },
})
```

當映射的計算屬性的名稱與 `state` 名稱相同時，我們也可以給 `mapState` 輔助函式傳一個字串陣列。

```javascript
// Component.vue

computed: mapState(['count', 'total']),
```

### 3. 區域狀態

使用 Vuex 並不意味著你需要將所有的狀態放入 Vuex。如果有些狀態嚴格屬於單個元件，最好還是作為元件的區域狀態。

另外，如果我們有區域計算屬性要用，可以使用 `...` 展開運算子將 `mapState` 輔助函式合併：

```javascript
// Component.vue

computed: {
  localComputed () {
    // ...
  },
  // 展開回傳的物件，將它混入到 computed 物件中
  ...mapState(['count', 'total'])
}
```

**CodePen Demo：**[Vue 2.x - Vuex mapState ](https://codepen.io/CHUPAIWANG/pen/JjKYLOo)

## Mutations

前面有提到，`state` 狀態不能直接改變。

舉例來說：
```html
<button @click="$store.state.count += 1">++</button>
```
雖然這樣不會拋出錯誤，也不會跳出警告，但這是錯誤的非法操作。

{{< notice warning >}} 
如果想要狀態非 `mutation` 函式引起的更新拋出錯誤，可以使用 [**嚴格模式**](https://vuex.vuejs.org/zh/guide/strict.html)。
{{< /notice >}}

我們只能透過 Vuex 中的 [`mutation`](https://vuex.vuejs.org/zh/api/#mutations) 來更新 `state`。雖然這種操作較繁瑣，但可以集中監控狀態的變化，避免不可預期情況發生。

定義 `mutation`：
```javascript
// store.js

mutations: {
  increment(state) {
    state.count += 1; // 變更狀態
  }
}
```

### 1. 提交 Mutation

`mutation` 類似事件監聽器，無法直接呼叫，我們必須透過 [`store.commit`](https://vuex.vuejs.org/zh/api/#commit) 方法來呼叫。

```javascript
// Component.vue

methods: {
  increment() {
    this.$store.commit('increment');
  },
}
```

**CodePen Demo：**[Vue 2.x -Vuex Mutations](https://codepen.io/CHUPAIWANG/pen/GRqoZYR)


### 2. Payload

可以向 `store.commit` 傳入額外的參數，即 `mutation` 的第二個參數 `payload`：
```javascript
// store.js

mutations: {
  increment(state, n) {
    state.count += n
  }
}
```
```javascript
$store.commit('increment', 10);
```

在大多數情況下，`payload` 應該是一個物件，這樣可以包含多個字段並且記錄的 `mutation` 會更易讀：
```javascript
// store

mutations: {
  increment(state, payload) {
    state.count += payload.amount;
  }
}

// 或是使用解構賦值
mutations: {
  increment(state, { amount }) {
    state.count += amount;
  }
}
```
```javascript
$store.commit('increment', {amount: 10});
```

**CodePen Demo：**[Vue 2.x -Vuex Mutations Payload](https://codepen.io/CHUPAIWANG/pen/wvWMGRW)


### 3. 物件風格的提交方式

提交 `mutation` 的另一種方式是直接使用包含 `type` 屬性的物件：
```javascript
$store.commit({
  type: 'increment',
  amount: 10
});
```

### 4. mapMutations 

`mutation` 一樣有 [`mapMutations`](https://vuex.vuejs.org/zh/api/#mapmutations) 輔助函式可以使用：
```javascript
// Component.vue

import { mapMutations } from 'vuex';

// 物件
methods: {
  ...mapMutations({
    increment: 'increment',
  }),
}

// 陣列
methods: {
  ...mapMutations(['increment']),
}
```

`mapMutations` 輔助函式也支持 `payload`，只需要在呼叫的地方傳入參數，會自動傳遞：
```html
<button @click="decrement(10)">++</button>
```

**CodePen Demo：**[Vue 2.x -Vuex mapMutations](https://codepen.io/CHUPAIWANG/pen/RwRraOw)


### 5. 使用常數替代 Mutation 類型

當 Vuex 管理的狀態越多，我們就會需要用到大量的 `mutation` 去更新，你不可能記住全部，因此可能會反覆查看。

為了解決上述問題，使用常數替代 `mutation` 類型事最常見的方案之一，可以使 `mutation` 更容易識別。


```javascript
// mutation-types.js
export const EDIT_TASK = 'EDIT_TASK';
...
```
```javascript
// store.js

import Vuex from 'vuex';
import { EDIT_TASK } from './mutation-types';

export default new Vuex.Store({
  state: {
    task: '';
  },
  mutations: {
    [EDIT_TASK](state, { value }) {
      state.task = value;
    },
  },
});
```

```javascript
// Component.vue

import { EDIT_TASK } from '../mutation-types';

// ...
methods: {
  [EDIT_TASK]() {
    this.$store.commit(EDIT_TASK, {});
  },
}
```

若要一次性導入，可以改成：
```javascript
import * as types from './mutation-types';

// mutations　...
[types.EDIT_TASK](state) {}
```

### 6. 響應規則

之前提到，`store` 與 `data` 一樣需要遵守 Vue 的響應規則。

因此物件新增屬性時，需要使用 `Vue.set()` 來處理：
```javascript
// store.js
import Vue from 'vue';

// ...
state: {
  obj: {};
},
mutations: {
  ADD_OBJ(state, { prop, value }) {
    Vue.set(state.obj, prop, value);
  }
}
```

或者運用展開運算子替換舊物件：
```javascript
mutations: {
  ADD_OBJ(state, { value }) {
    state.obj = { ...state.obj, newProp: 'value' };
  }
}
```

陣列修改也一樣，也需要使用 `Vue.set()` 來處理：
```javascript
state: {
  list: ['a', 'b', 'c', 'd'],
},
mutations: {
  EDIT_LIST(state, { index, value }) {
    Vue.set(state.list, index, value);
  }
}
```

詳細可以參考 [**深入響應式原理**](https://cn.vuejs.org/v2/guide/reactivity.html)。


### 7. 非同步處理

當我們使用 `mutation` 更新狀態時，必須是同步處理，否則 devtool 工具將無法追蹤到狀態的改變。

## Actions

如果我們要非同步處理狀態資料，就無法直接使用 `mutation`，而必須透過 [`action` ](https://vuex.vuejs.org/zh/api/#actions)間接的提交 `mutation` 來變更狀態資料。

定義一個簡單的非同步處理操作的 `action`：
```javascript
// store.js

actions: {
  incrementAsync(context) {
    setTimeout(() => {
      context.commit('increment');
    }, 1000);
  }
}
```

`actions` 函式接受一個與 `store` 實體具有相同方法和屬性的 `context` 物件，包含以下屬性：
```javascript
{
  state,      // 等同於 store.state，若在模組中則為區域狀態
  rootState,  // 等同於 store.state，只存在於模組中
  commit,     // 等同於 store.commit
  dispatch,   // 等同於 store.dispatch
  getters,    // 等同於 store.getters
  rootGetters // 等同於 store.getters，只存在於模組中
}
```
因此我們可以用來呼叫 `context.commit` 提交一個 `mutation`，或者透過 `context.state` 和 `context.getters` 來獲取 `state` 和 `getters` 物件。

可以使用 ES6 解構賦值來簡化程式碼：
```javascript
// store.js

actions: {
  incrementAsync({ commit }) {
    setTimeout(() => {
      commit('increment');
    }, 1000);
  },
}
```

### 1. 調度 Action

`action` 是透過 [`store.dispatch`](https://vuex.vuejs.org/zh/api/#dispatch) 方法來呼叫：
```javascript
// Component.vue

methods: {
  incrementAsync() {
    this.$store.dispatch('incrementAsync');
  },
}
```
**CodePen Demo：**[Vue 2.x -Vuex Actions](https://codepen.io/CHUPAIWANG/pen/vYKLKjX)


`action` 同樣有 `payload` 參數和物件風格，也有 [`mapActions`](https://vuex.vuejs.org/zh/api/#mapactions) 輔助函式可以使用，用法與 `mutation` 差不多，這裡就不多加說明。


### 2. 組合 Action

因為 `action` 通常用來處理非同步操作，所以我們可以回傳 Promise，用來處理更佳複雜的非同步流程。

```javascript
// store.js

actions: {
  actionA({ commit }) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        commit('someMutation');
        resolve();
      }, 1000);
    });
  },
}
```

## Getters

你可以將 [Getters](https://vuex.vuejs.org/zh/api/#getters) 看成全域的計算屬性。

有時候我們需要從 State 中衍生一些狀態，例如對陣列過濾，假設有很多元件都會用到，那麼我們就定義 `getter`，它與計算屬性一樣會緩存結果，只有當它的依賴值發生了改變才會被重新計算。

```javascript
// store.js

state: {
  numbers: [1, 2, 3, 4, 5, 6, 7, 8],
},
getters: {
  even(state) {
    return state.numbers.filter((n) => n % 2 === 0);
  },
}
```
`getter` 函式的第一個參數為 `state` 物件，若希望傳入其他 `getter`，可以使用第二個參數 `getters` 物件：
```javascript
// store.js

getters: {
  even(state) {
    // ...
  },
  evenCount(state, getters) {
    return getters.even.length;
  },
}
```

### 1. 訪問

要在元件中訪問 `getter` 一樣可以透過 `$stroe` 實體：
```javascript
// Component.vue

computed: {
  even() {
    return this.$store.getters.even;
  }
}
```
**CodePen Demo：**[Vuex Getters](https://codepen.io/CHUPAIWANG/pen/RwRWzyK)

或者使用 `mapGetters` 輔助函數：
```javascript
// Component.vue

import { mapGetters } from 'vuex';

//...
computed: {...mapGetters(['even']) },
```