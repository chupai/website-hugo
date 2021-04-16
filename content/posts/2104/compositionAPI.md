---
title: "Vue 3 - Composition API"
date: 2021-04-13
description: "Composition API 是 Vue 3 的最大特點，為 Vue 元件的另一種編寫方式。"
keywords: ["Vue3", "Composition API", "Vue 3 全新功能"]
tags: ["Vue3", "Composition API", "Vue 3 全新功能"]
series: ["Vue3 筆記"]
image: images/covers/2104/compositionAPI.png
draft: false
---

<!--more-->

{{< featuredImage >}}

## 前言

**Composition API（組合式 API）** 是 Vue 3 的最大特點，為 Vue 元件的另一種編寫方式。

首先，來探討 Options API 遇到的問題。

Options API 結構：
```javascript
export default {
  props: {},
  data() {
    return {};
  },
  watch: {},
  computed: {},
  methods: {},
};
```

隨著專案複雜度提高，程式碼會不斷膨脹，後續的維護成本會逐漸增加，因此我們會想要將功能切割，提升程式碼的複用性及維護性。但 Options API 形成了一種強制的約定，導致邏輯分散在各個選項中，造成程式碼難以閱讀理解及分割，雖然有 `mixins`，但大量使用時，容易出現命名衝突、來源不清晰等問題。

**Composition API 改善的問題**：
- 程式能依功能分類使用，增加可讀性；
- 封裝功能，可跨元件使用，增加複用性；
- 提供更好的 TypeScript 支持。

## 入口函式 Setup

全新的 [`setup`](https://v3.cn.vuejs.org/api/options-composition.html#setup) 選項為一個函式，它會在元件實體尚未被建立**之前**執行，是使用 Composition API 實際位置。

`setup` 函式可以回傳一個物件，物件中的內容都將暴露給元件的其餘部分（計算屬性、方法、生命週期鉤子等等）以及元件的模板：

```vue
<script>
  export default {
    setup() {
      const msg = 'hello!';
      const handler = () => {
        alert(msg);
      };
      return { msg, handler };
    },
  };
</script>

<template> 
  <div>
    <p>{{ msg }}</p>
    <button @click="handler">click</button>
  </div>
</template>
```
**CodePen Demo：**[Vue 3.0 - setup 選項](https://codepen.io/CHUPAIWANG/pen/xxgdNYN)

Composition API 可以選擇想要暴露的 property，不同於 Options API 全部都暴露給實體。另外，還可以透過解構重新命名，避免命名衝突的發生。

## 建立響應式資料

在 Vue 3 中，可以透過 [`reactive`](https://v3.cn.vuejs.org/api/basic-reactivity.html#reactive) 方法建立響應式物件，或者透過 [`ref`](https://v3.cn.vuejs.org/api/refs-api.html#ref) 方法建立響應式變數。

### 1. Reactive

[`reactive()`](https://v3.cn.vuejs.org/api/basic-reactivity.html#reactive) 接受一個普通物件作為參數，回傳一個響應式的物件狀態。該響應式轉換是「深度轉換」，會影響巢狀物件傳遞的所有 property。

>Vue 3 的響應式的物件是基於 ES6 的 [Proxy](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Proxy) 實現。而 Vue 2 是透過 ES5  的 [`Object.defineProperty()`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) 實現。

```vue
<script>
  import { reactive } from 'vue';

  export default {
    setup() {
      const data = reactive({
        msg: 'hello!',
      });
      const handler = () => {
        alert(data.msg);
      };
      return { data, handler };
    },
  };
</script>
<template> 
  <div>
    <p>{{ msg }}</p>
    <div>
      <input type="text" v-model="msg">
    </div>
    <button @click="handler">click</button>
  </div>
</template>
```
**CodePen Demo：**[Vue 3.0 - reactive 響應式物件](https://codepen.io/CHUPAIWANG/pen/MWJQjEP)

>`reactive()` 相當於 Vue 2.6 新增的 [`Vue.observable()`](https://cn.vuejs.org/v2/api/#Vue-observable) API，為避免與 RxJS 中的 observables 混淆因此對其重新命名。

另外，`data` 選項回傳一個物件時，在內部也是透過 `reactive()` 使其成為響應式物件的。

### 2. Ref

`ref()` 接受一個任何型別的參數，回傳一個響應式且可變的 Ref 物件。Ref 物件只包含一個名為 `value` 的屬性。

>若傳入物件型別，內部會呼叫 `reactive()` 將其轉成響應式物件。

從 `setup` 回傳的 Ref 物件在模板中訪問時是被自動解開的，因此不需要在模板中使用 `.value`。

```vue
<script>
  import { ref } from 'vue';

  export default {
    setup() {
      const msg = ref('hello!');
      const handler = () => {
        alert(msg.value); // 透過 .value 訪問值
      };
      return { msg, handler };
    },
  };
</script>

<template> 
  <div>
    <p>{{ msg }}</p>
    <div>
      <input type="text" v-model="msg">
    </div>
    <button @click="handler">click</button>
  </div>
</template>
```
**CodePen Demo：**[Vue 3.0 - 帶 ref 的響應式變數](https://codepen.io/CHUPAIWANG/pen/PoWmMbg)


### 3. Ref 自動解構

如果將 Ref 物件分配給響應式物件 的 property 時，Ref 物件會自動解構。

```javascript
import { ref, reactive } from 'vue';

const count = ref(1);

const data = reactive({});
data.count = count;
console.log(data.count);  // 1

const obj = ref({});
obj.value.count = count;
console.log(obj.value.count);  // 1
```

但要注意，只有物件會解構 Ref 物件，陣列不會。陣列給值仍然需要加 `.value`。
```javascript
import { ref, reactive } from 'vue';

const arr = ref([]);
arr.value[0] = count;
console.log(arr.value[0]);  // Ref{}
console.log(arr.value[0].value);  // 1
arr.value[1] = count.value;
console.log(arr.value[1]);  // 1
```

**CodePen Demo：**[Vue 3.0 - 自動解構](https://codepen.io/CHUPAIWANG/pen/VwPQmEQ)

### 4. Reactive vs Ref

基本型別，肯定只能使用 `ref()` 宣告，那麼複雜型別呢？究竟是要使用 `ref()` 還是 `reactive()`？

有些人認為複雜型別就用 `reactive()` 就好了啊，少一層 `.value` 多方便。但當你遇到需要清空物件時，你無法直接指定空物件，這時你只能改用 `ref()` 宣告。

>為了風格統一，我個人是建議全部使用 `ref()` 宣告響應式資料。不過如果你有其他看法，可以留言討論。


## setup 參數

執行 `setup` 函式時，元件實體尚未被建立，在 `setup` 函式內的 `this` 不是實體的引用，若要訪問 `props`、`attrs`、`slots`、`emit` 可以透過 `setup` 函式提供的兩個參數來訪問：
- `props`
- `context`

### 1. Props

`props` 為響應式物件，可以用來在 `setup` 函式中取得父元件傳遞的 prop。

```javascript
export default {
  props: ['msg'],
  setup(props) {
    console.log(props.msg);
  },
};
```
**CodePen Demo：**[Vue 3.x  - props 參數](https://codepen.io/CHUPAIWANG/pen/poRpbpW)


>在模板中雖然可以直接取得 prop，但使用 Composition API 時，會建議在 `setup()` 中回傳，這樣可以清楚明白資料的來源。

```vue
<script>
  export default {
    props: ['msg'],
    setup(props) {
      return { props };
    },
  };
</script>

<template> 
  <div>
    <p>{{ props.msg }}</p>
  </div>
</template>
```

注意，如果想要使用 ES6 的解構 `props` 取 prop，會失去響應性。

響應式物件無法直接使用 ES6 的解構，會直接失去響應式特性。因此 Vue 3.x 提供了 [`toRefs`](https://v3.cn.vuejs.org/api/refs-api.html#torefs) 方法，它會將響應式物件轉成普通物件並將每個 property 轉成指向原始物件相應 property 的 Ref 物件。

```javascript
import { toRef } from 'vue';

export default {
  props: ['msg', 'name'],
  setup(props) {
    const { name: propsMsg, msg: propsName } = toRefs(props);
    return { propsMsg, propsName };
  },
};
```
**CodePen Demo：**[Vue 3.x  - toRefs](https://codepen.io/CHUPAIWANG/pen/VwPQLNd)

如果 prop 是可選的，那麼傳入的 `props` 中可能會沒有該 prop，`toRefs()` 不會為不存在的 prop 建立一個 Ref 物件，這種情況下，可以改使用 [`toRef`](https://v3.cn.vuejs.org/api/refs-api.html#toref) 方法處理。

`toRef()` 與 `ref()` 雖然都是用建立響應式資料，但 `ref` 會複製一份新的資料，而 `toRef()` 會保持對其源 property 的響應式連接。即使源 property 不存在，`toRef()` 也會回傳一個可用的 Ref 物件。
```javascript
import { toRef } from 'vue';

export default {
  props: {
    msg: {
      type: String,
      default: 'Hi!',
    },
  },
  setup(props) {
    const msg = toRef(props, 'msg');
    return { msg };
  },
};
```
**CodePen Demo：**[Vue 3.x  - toRef](https://codepen.io/CHUPAIWANG/pen/bGgLVLp)

### 2. Context

`context` 是一個普通的物件，它暴露三個元件的 property：
- `attrs` Attribute
- `slots` 插槽
- `emit` 觸發事件

`context` 不是響應式物件，可以安全地使用 ES6 解構：
```javascript
export default {
  setup(props, { attrs, slots, emit }) {
    // ...
  },
};
```

注意，`attrs` 和 `slots` 是有狀態的物件，會隨元件本身的更新而更新，要避免對它們進行解構。

## 計算 & 監聽

### 1. computed

#### 基本用法

[`computed`](https://v3.cn.vuejs.org/api/computed-watch-api.html#computed) 方法接受一個 getter 函式，會回傳一個唯讀的響應式 Ref 物件。
```javascript
import { ref, computed } from 'vue';

const count = ref(1);
const plusOne = computed(() => count.value + 1);

console.log(plusOne.value); // 2

plusOne.value++; // 錯誤
```

**CodePen Demo：**[Vue 3.0 - computed](https://codepen.io/CHUPAIWANG/pen/oNBEZxK)


#### setter

或者，使用具有 `get` 和 `set` 函式的物件，並回傳一個可寫的響應式 Ref 物件。
```javascript
import { computed } from 'vue';

const count = ref(1);
const plusOne = computed({
  get: () => count.value + 1,
  set: (newVal) => {
    count.value = newVal - 1;
  },
});

plusOne.value = 1;
console.log(count.value); // 0
```

**CodePen Demo：**[Vue 3.0 - computed - setter 應用](https://codepen.io/CHUPAIWANG/pen/NWdypdv)

### 2. watch

[`watch`](https://v3.cn.vuejs.org/api/computed-watch-api.html#watch) API 與實體方法 [`$watch`](https://v3.cn.vuejs.org/api/instance-methods.html#watch) 完全等效。

```javascript
watch(source, callback, [options])
```

#### 基本用法

資料源可以是一個 getter 函式或 Ref 物件。
```javascript
import { watch } form 'vue';

// 監聽一個 getter 函式
const state = reactive({ count: 0 });
watch(
  () => state.count,
  (newVal, oldVal) => {
    // ...
  },
);

// 監聽一個 ref
const count = ref(0);
watch(count, (newVal, oldVal) => {
  // ...
});
```

第一種用法可以監聽響應式物件的 property，不需要監聽整個物件。

**CodePen Demo：**[Vue 3.0 - watch](https://codepen.io/CHUPAIWANG/pen/ZELream)


#### 監聽多個資料源

還可以使用陣列同時監聽多個資料源：

```javascript
import { watch } form 'vue';

const foo = ref('');
const bar = ref('');

watch([foo, bar], ([newFoo, newBar], [oldFoo, oldBar]) => {
  // ...
});
```

#### 選項

- `deep`：深度監聽物件或陣列。
- `immediate`：立即觸發 callback。
- `flush`：控制 callback 的時間。
  - `'pre'`：預設值，渲染被呼叫
  - `'post'`：渲染後呼叫
  - `'sync'`：一旦值發生改變，callback 將同步呼叫。

### 3. watchEffect

[`watchEffect`](https://v3.cn.vuejs.org/api/computed-watch-api.html#watcheffect) 是 Vue 3.x 新增的，比起 `watch` 它不需要指定監聽資料源，會自動收集依賴。`watchEffect` 初始化會執行一次用以收集依賴。

```javascript
const count = ref(0);

watchEffect(() => {
  console.log(count.value);
});
```

`watchEffect` 會回傳一個 `WachStopHandle` 函式，用來停止 `watchEffect`。
```javascript
const stop = watchEffect(() => {
  // ...
});

stop();
```

## 生命週期鉤子

在 `setup` 函式中執行的程式，等同在 `beforeCreate` 和 `created` 階段執行，直接寫在 `setup` 函式內即可，其它生命週期鉤子需要透過 `on` 前綴的方法訪問。

| Options API | Hook inside `setup` | 
| -------- | -------- | 
| `beforeCreate` | - - - |
| `created` | - - - |
| `beforeMount` | `onBeforeMount` |
| `mounted` | `onMounted` |
| `beforeUpdate` | `onBeforeUpdate` |
| `updated` | `onUpdated` |
| `beforeUnmount` | `onBeforeUnmount` |
| `unmounted` | `onUnmounted` |
| `errorCaptured` | `onErrorCaptured` |
| `renderTracked` | `onRenderTracked` |
| `renderTriggered` | `onRenderTriggered` |

生命週期函式接收一個 `callback` 作為參數，當鉤子被元件呼叫時，callback 就會被執行。

```javascript
import { onMounted } from 'vue';

export default {
  setup() {
    // mounted
    onMounted(() => {
      // ...
    });
  },
};
```

## Provide / Inject

在 Composition API 中使用 [`provide/inject`](https://v3.cn.vuejs.org/api/options-composition.html#provide-inject)。

### 1. Provide

在 `setup()` 中使用 `provide` 時，需要使用到 `provide` 方法。

`provide()` 接受兩個參數，用來定義要傳遞的資料：
- `name` 定義名稱（字串）
- `value` 定義值

```javascript
import { provide } from 'vue';

export default {
  setup() {
    provide('count', 1);
  },
};
```

### 2. Inject

在 `setup()` 中使用 `inject` 時，需要使用到 `inject` 方法。

`provide()` 接受兩個參數，回傳注入的資料：
- `name` 資料名稱（字串）
- `value` 預設值（可選）

```javascript
import { inject } from 'vue';

export default {
  setup() {
    const count = inject('count', 0);
    return { count };
  },
};
```

### 3. 響應性

`provide/inject` 綁定的值預設並不是響應式的。我們可以通過傳遞響應式資料來處理 `provide/inject` 之間的響應性。

可以使用 `reactive` 傳遞響應式物件，或者使用 `ref` 傳遞 Ref 物件。

```javascript
import { ref, provide } from 'vue';

export default {
  setup() {
    const count = ref(1);
    provide('count', count);
  },
};
```

### 4. 操作響應式

`provide/inject` 不像 Vuex 一樣可以追蹤狀態改變，如果資料狀態操作分散在多個後代元件中，會很難維護。

>因此，建議盡可能在定義 `provide` 的元件上操作 響應式的 `provide/inject` 資料。

若一定要在注入資料的元件內操作。這種情況下，建議多定義一個負責操作的方法。
```javascript
import { ref, provide } from 'vue';

export default {
  setup() {
    const count = ref(1);
    const addCount = ()=> {
      count.value += 1;
    };
    
    provide('count', count);
    provide('addCount', addCount);
  },
};
```
```javascript
import { inject } from 'vue';

export default {
  setup() {
    const count = inject('count', 0);
    const addCount = inject('addCount');
    return { count, addCount };
  },
};
```

如果要確保 `provide` 傳遞的資料不被後代元件更改，可以使用 [`readonly`](https://v3.cn.vuejs.org/api/basic-reactivity.html#readonly) 或者 [`computed`](https://v3.cn.vuejs.org/api/computed-watch-api.html#computed) 方法建立唯讀資料。

## 模板引用

### 1. Composition API

在 Composition API 中，只需要使用 `ref()` 宣告一個與 `ref` attribute 相同名稱的變數、內容值為 `null`，並將它回傳，即可獲取 DOM。

```javascript
import { ref, onMounted } from 'vue'; 

export default {
  setup() {
    const input = ref(null);
    
    onMounted(()=>{
      const dom = input.value;
      dom.focus();
    });
    
    return { input };
  },
};
```
**CodePen Demo：**[Vue 3.x  - Composition API 模板引用](https://codepen.io/CHUPAIWANG/pen/PoWROqx)

### 2. v-for 的處理

>`v-for` 模板引用 Vue 2.x 與 Vue 3.x 的差異可以參考 [**模板指令：v-for 中的 Ref 陣列**](https://chupai.github.io/posts/2103/templatedirectives/)。

Composition API：
```vue
<template> 
  <div v-for="n of nums" :ref="setDoms">{{ n }}</div>
  <button type="button" @click="nums.push(nums.length + 1)">+</button>
</template>

<script>
console.clear();

import { ref, onMounted, onBeforeUpdate, onUpdated } from 'vue'

export default {
  setup() {
    const nums = ref([1, 2, 3]);
    const doms = ref([]);
    const setDoms = (el) => {
      if (el) {
        doms.value.push(el);
      }
    };
    onMounted(() => {
      console.log(doms.value);
    });
    onBeforeUpdate(() => {
      doms.value = [];
    });
    onUpdated(() => {
      console.log(doms.value);
    });
    return { nums, setDoms };
  },
};
</script>
```

**CodePen Demo：**[Vue 3.0  - 模板引用 v-for 的處理](https://codepen.io/CHUPAIWANG/pen/BaprroM)


## 邏輯拆分

Composition API 最主要的目的就是將邏輯功能拆分出來，並在入口函式 `setup` 中組合使用。

舉個例子，下面為一個簡單的 TodoAdd 元件，用來新增 TodoList 資料狀態。
```javascript
// TodoAdd.vue
import { ref } from 'vue';

export default {
  emits: ['addTodo'],
  setup(props, { emit }) {
    const content = ref('');
    function emitAddTodo() {
      const todo = {
        id: new Date().getTime(),
        content: content.value,
        isCompleted: false,
      };
      if (content.value) {
        emit('addTodo', todo);
      }
      content.value = '';
    }
    return { content, emitAddTodo };
  },
};
```

### 1. 簡易拆分

將程式碼根據邏輯功能抽離成函式：
```javascript
// TodoAdd.vue
import { ref } from 'vue';

export default {
  emits: ['addTodo'],
  setup(props, { emit }) {
    const { content, emitAddTodo } = useEmitAddTodo(emit);
    return { content, emitAddTodo };
  },
};

function useEmitAddTodo(emit) {
  const content = ref('');
  function emitAddTodo() {
    const todo = {
      id: new Date().getTime(),
      content: content.value,
      isCompleted: false,
    };
    if (content.value) {
      emit('addTodo', todo);
    }
    content.value = '';
  }

  return { content,emitAddTodo };
}
```

### 2. 抽離成模組

也可將程式碼抽離成模組，讓其他元件使用。

建立一個 composables 資料夾，用來放置功能程式碼。

```javascript
// addTodo.js

import { ref } from 'vue';

const addTodo = function(emit) {
  const content = ref('');
  function emitAddTodo() {
    const todo = {
      id: new Date().getTime(),
      content: content.value,
      isCompleted: false,
    };
    if (content.value) {
      emit('addTodo', todo);
    }
    content.value = '';
  }

  return { content, emitAddTodo };
};

export default addTodo;
```

在元件中引用：
```javascript
// TodoAdd.vue
import addTodo from '@/composables/useTodo.js';

export default {
  emits: ['addTodo'],
  setup(props, { emit }) {
    const { content, emitAddTodo } = addTodo(emit);
    return { content, emitAddTodo };
  },
};
```