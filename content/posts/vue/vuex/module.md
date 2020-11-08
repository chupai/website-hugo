---
title: "Vuex - Module"
date: 2020-10-28
description: "Vuex 模組化。"
keywords: ["Vue","Vuex","竹白"]
tags: ["Vue", "Vuex"]
series: ["竹白的 Vue 記事本"]
image: images/covers/vue/vuex/module.png
draft: false
---

>[竹白的 Vue 記事本 目錄](/posts/vue/menu/)

<!--more-->
{{< featuredImage >}}

## 模組化

如果所有狀態資料都集中在一個比較大的物件，會遇到一個問題：當應用變得非常複雜時，`store` 物件就有可能變得相當難以維護。

為了解決以上問題，Vuex 允許我們將 `store` 分割成模組（module）。

## 分割

每個模組就是一個小型的 `store`，擁有自己的 `state`、`mutations`、`actions`、`getters`、甚至是巢狀子模組。

兩種模組結構參考：
```javascript
// moduleA.js

export default {
  namespaced: true,
  state: () => ({
    a1: 'aaa',
  }),
  mutations: {},
  actions: {},
  getters: {},
};
```
```javascript
// moduleB.js

const state = () => ({
  b1: 'bbb',
});

const getters = {};
const actions = {};
const mutations = {};

export default {
  state,
  getters,
  actions,
  mutations,
};
```
為了避免模組重用時，狀態物件通過引用被共享，所以會使用函式型式來宣告模組的狀態。

引入：
```javascript
// store.js

import Vuex from 'vuex';
import moduleA from './modules/modulesA';
import moduleB from './modules/modulesB';

export default new Vuex.Store({
  state: {},
  mutations: {},
  actions: {},
  modules: {
    moduleA: moduleA,
    moduleB: moduleB,
    
    // 或是使用簡寫
    // moduleA,
    // moduleB,
  },
});
```

### 1. 取得模組狀態

模組的的 `state` 物件將被儲存在 `moduleA`、` moduleB` 中：
```javascript
store.state.moduleA; // { "a1": 'aaa' }
store.state.moduleB; // { "b1": 'bbb' }
```

**CodePen Demo：**[Vue 2.x -Vuex Module](https://codepen.io/CHUPAIWANG/pen/YzWqQNm)


## 模組的區域狀態

對於模組內的 `mutation`、`getter`、`action`，透過參數取得的狀態為模組內部的區域狀態。

```javascript
const moduleA = {
  state: () => ({
    text: '區域'
  }),
  mutations: {
    print(state) {
      console.log(state.text);
    },
  },
};

const store = new Vuex.Store({
  state: {
    text: '全域',
  },
  modules: {
    a: moduleA,
  },
});
```

```javascript
store.commit('print');   //  '區域'
```

**CodePen Demo：**[Vue 2.x -Vuex Module 區域狀態](https://codepen.io/CHUPAIWANG/pen/yLJORxa)

若要取的根 ` state`，`getter` 函式可以使用第三個參數 `rootState`，`action` 函式也可以透過 `context.rootState` 取得：
```javascript
const moduleA = {
  // ...
  getters: {
    foo(state, getters, rootState) {
      // ...
    },
  },
  actions: {
    boo({ rootState }) {
      // ...
    },
  },
}
```
另外，還有 `rootGetters` 可以取得根 `getters`。

## 命名空間

從上面可知，預設情況下模組內的 `action`、`mutation` 和 `getter` 是註冊在全域空間下。如果全域、區域有相同的名稱的函式都會執行。

```javascript
const moduleA = {
  state: () => ({
    text: '區域'
  }),
  mutations: {
    print(state) {
      console.log(state.text);
    },
  },
};

const store = new Vuex.Store({
  state: {
    text: '全域',
  },
  mutations: {
    print(state) {
      console.log(state.text);
    },
  },
  modules: {
    a: moduleA,
  },
});
```
```javascript
store.commit('print');
// '全域'
// '區域'
```
**CodePen Demo：**[Vue 2.x -Vuex Module 相同名稱](https://codepen.io/CHUPAIWANG/pen/LYZNXev)


因此，如果你希望模組具有更高度的封裝性，只讓它們在內部有作用，可以設置 `namespaced: true` 使其成為帶命名空間的模組。

```javascript
const moduleA = {
  namespaced: true,
  // ...
};
```

啟用命名空間後，如果要呼叫模組內的 `mutation` 就需要加上路徑：
```javascript
store.commit('moduleA/print');
// '區域'

// 呼叫相同名稱的全域 mutation 也不會觸發模組內的函式
store.commit('print');
// '全域'
```

`getter` 和 `action` 也一樣要加上路徑：
```javascript
// getter
store.getters['moduleA/xxxx'];
// action
store.dispatch('moduleA/xxxx');
```

**CodePen Demo：**[Vue 2.x -Vuex Module 命名空間](https://codepen.io/CHUPAIWANG/pen/PozNXOE)

### 1. 訪問全域內容

使用命名空間後，在模組內呼叫的 `mutation` 和 `action` 都會是區域的。但如果我們要在模組內操作全域的 `mutation` 和 `action` 該怎麼做呢？

你可以將 `{ root: true }` 作為 `dispatch` 或 `commit` 方法的第三參數傳入：
```javascript
dispatch('someOtherAction', null, { root: true });
commit('someMutation', null, { root: true });
```

**CodePen Demo：**[Vue 2.x -Vuex Module 訪問全域內容](https://codepen.io/CHUPAIWANG/pen/eYzzYxL)

### 2. 在命名空間的模組下全域註冊

若你希望在命名空間的模組上，某個函式全域註冊，你可以使用物件的形式定義，並加上 `root: true`
```javascript
const moduleA = {
  namespaced: true,
  actions: {
    foo: {
      root: true,
      handler (context, payload) {
        // ...
      },
    }
  },
};
```

### 3. 輔助函式

如果要使用輔助函式引入模組的內容，寫起來會有點繁瑣：
```javascript
// Component.vue

computed: {
  ...mapState({
    a1: (state) => state.moduleA.a1,
    a2: (state) => state.moduleA.a2,
  })
},
methods: {
  ...mapActions({
    foo: 'moduleA/foo',
    boo: 'moduleA/boo',
  }),
}
```

**CodePen Demo：**[Vue 2.x -Vuex Module 輔助函式](https://codepen.io/CHUPAIWANG/pen/bGeeNbr)

你可以將模組的空間名稱作為第一個參數傳入輔助函式，這樣所有綁定會自動將該模組作為上下文，這樣就能使用陣列型式映射內容，簡化上述繁瑣的程式碼：

```javascript
// Component.vue

computed: {
  ... mapState('moduleA', ['a1', 'a2']),
},
methods: {
  ... mapActions('moduleA', ['foo', 'boo']),
}
```

**CodePen Demo：**[Vue 2.x -Vuex Module 輔助函式 - 模組命名參數](https://codepen.io/CHUPAIWANG/pen/jOrrEPz)

或者透過 [`createNamespacedHelpers`](https://vuex.vuejs.org/zh/api/#createnamespacedhelpers) 建立基於模組命名空間的輔助函式：
```javascript
// Component.vue
import { createNamespacedHelpers } from 'vuex';
const { mapState, mapActions } = createNamespacedHelpers('moduleA');

export default {
  computed: {
    ... mapState(['a1', 'a2']),
  },
  methods: {
    ... mapActions(['foo', 'boo']),
  }
}
```

**CodePen Demo：**[Vue 2.x -Vuex Module 輔助函式 - createNamespacedHelpers](https://codepen.io/CHUPAIWANG/pen/ZEOOGwL)

