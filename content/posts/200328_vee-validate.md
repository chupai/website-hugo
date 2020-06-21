---
title: "Vue筆記 - 表單驗證套件 VeeValidate"
date: 2020-03-28
description: "這週是 六角鼠年鐵人賽 第八週，紀錄 Vue 表單驗證套件 - Vee Validate 操作方法。"
keywords: ["VeeValidate", "Vue", "w3HexSchool","竹白"]
tags: ["VeeValidate", "Vue", "w3HexSchool"]
categories: ["w3HexSchool六角鼠年鐵人賽","Vue筆記"]
draft: false
---

這週是 **六角鼠年鐵人賽** 第八週，紀錄 Vue 表單驗證套件 - Vee Validate 操作方法。

## 前言

>[VeeValidate  官網](https://logaretm.github.io/vee-validate/)

VeeValidate 是專用於 Vue.js 的表單驗證套件。它有很多預設驗證規則，也支持自定義驗證規則。

由於 2.x版本 與 3.x版本 的操作非常不同，所以簡單紀錄一下。

##  VeeValidate 2

### 1. 安裝
安裝舊版本：
```
npm install vee-validate@2.2.15 --save
```

導入：
```javascript
// main.js
import VeeValidate from 'vee-validate';
Vue.use(VeeValidate);
```
#### 1.1 會遇到的問題

如果 Focus 到 `input` 標籤再點外面，不會進行驗證，可以修改 `events`，加上 `blur`：
```javascript
// main.js

Vue.use(VeeValidate, {
  events: 'input|blur',
});
```

#### 1.2 中文化

舊方法 2.1.0-beta.24版之前：
```javascript
// main.js

import VeeValidate from 'vee-validate';
import zhTW from 'vee-validate/dist/locale/zh_TW';

VeeValidate.Validator.localize('zhTW', zhTW);
Vue.use(VeeValidate);
```

新方法，必須先安裝 `vue-i18n`：
```
npm install vue-i18n --save
```

```javascript
// main.js
import VeeValidate from 'vee-validate';
import zhTW from 'vee-validate/dist/locale/zh_TW';
import VueI18n from 'vue-i18n';

Vue.use(VueI18n);

const i18n = new VueI18n({
  locale: 'zhTW',
});

Vue.use(VeeValidate, {
  events: 'input|blur',
  i18n,
  dictionary: {
    zhTW,
  },
});

// 記得載入 i18n
new Vue({
  i18n,
  router,
  render: (h) => h(App),
}).$mount('#app');
```

### 2. 基本用法

```html
<input
  type="text"
  name="name"
  placeholder="輸入姓名"
  v-validate="'required'"
  :class="{ 'is-invalid': errors.has('name') }"
/>
<span v-if="errors.has('name')">請輸入姓名</span>
```

- `input` 標籤內要設定 `name` 這個特性，特性值的部分則是根據該欄位性質做設定，例：姓名就是 `name`，地址就是 `address`。
- 加上 `v-validate="'required'"`，表示這一個欄位是必填的。
- `errors.has('name')` ，填入 `name` 特性值。初始值為 `fasle`，當觸發 `input` 標籤時，就會開始驗證，如果欄位未填寫值為 `true`。
  - 可以配合 `v-if` 顯示提式字；
  - 或是搭配 `:class` 加入提式樣式。

假設要驗證 email 格式，則可以使用 `v-validate="'required|email'"`。

而 `errors.first('email')` 它會根據 email 驗證的各種狀態來顯示不同的提示文字。

#### 2.1 獲取錯誤訊息
- `errors.first('field')`：獲取關於當前 `field` 的第一個錯誤訊息
- `collect('field')`：獲取關於當前 `field` 的所有錯誤訊息
- `has('field')`：當前 `filed` 是否有錯誤
- `all()`：當前表單所有錯誤
- `any()`：當前表單是否有任何錯誤

### ３. 表單提交前的處理

最後我們還需要設定表單驗證方法，來避免送出錯誤的資料格式：
```javascript
this.$validator.validate().then((result) => {
  if (result) {
    // 當驗證成功時執行 AJAX 的行為
  } else {
    // 驗證失敗產生的行為
  }
});
```

## VeeValidate 3

VeeValidate 在 v3.x版本之後的使用方法與之前完全不同。

### 1. 安裝

安裝最新版本 `vee-validate`：
```
npm install vee-validate --save
```

導入：
```javascript
// main.js
import { ValidationObserver, ValidationProvider, extend, localize} from 'vee-validate';
import * as rules from 'vee-validate/dist/rules';
import TW from 'vee-validate/dist/locale/zh_TW.json';

Object.keys(rules).forEach((rule) => {
  extend(rule, rules[rule]);
});

localize('zh_TW', TW);

Vue.component('ValidationObserver', ValidationObserver);
Vue.component('ValidationProvider', ValidationProvider);
```
安裝內容會在下方依序說明。

### 2. 添加規則

預設情況下，VeeValidate 不附帶任何驗證規則，這是為了使檔案輕量。

首先導入定義規則所需的 `extend` 函式：
```javascript
import { extend } from 'vee-validate';

// rules
```
接下來，就是加入規則。

#### 2.1 安裝所有規則

安裝所有規則有兩種遍歷作法：

```javascript
import { extend } from 'vee-validate';
import * as rules from 'vee-validate/dist/rules';

// 作法一
Object.keys(rules).forEach((rule) => {
  extend(rule, rules[rule]);
});

// 作法二(with typescript)
for (let [rule, validation] of Object.entries(rules)) {
  extend(rule, {
    ...validation,
  });
}
```

除了上述方法，另一種方法是安裝 `vee-validate` 的完整捆綁包，而不是預設捆綁包，該捆綁包預裝了所有驗證規則及其英文消息。

將 `'vee-validate'` 替換成 `'vee-validate/dist/vee-validate.full.esm'`，就可以一次安裝所有驗證規則，而不用導入 `extend` 函式。

```javascript
import { ValidationObserver, ValidationProvider } from 'vee-validate/dist/vee-validate.full.esm';
```

{{< notice warning >}}
但不建議一次安裝全部規則，應該使用下面的方式，按照需求安裝。
{{< /notice >}}

#### 2.2 導入規則

為了避免一次加載全部規則，我們可以選擇需求來導入規則：
```javascript
import { extend } from 'vee-validate';
import { required, email } from 'vee-validate/dist/rules';  // 按需求導入規則

extend('required', required);
extend('email', email);
```

更多可用規則可以參考 [官方文件](https://logaretm.github.io/vee-validate/guide/rules.html#rules)。

### 3. 基本用法

導入 `ValidationProvider` 元件：
```javascript
import { ValidationProvider } from 'vee-validate';

// 在 main.js 全域註冊元件
Vue.component('ValidationProvider', ValidationProvider);  

// 區域註冊元件（推薦）
export default {
  components: {
    ValidationProvider,
  },
};
```

2.X 版原本是添加 `v-validate`，而 3.X版則改用  `ValidationProvider` 元件進行包裹：
```html
<ValidationProvider v-slot="{ failed, errors }" name="name" rules="required">
    <label for="username">*收件人姓名</label>
    <input
      type="text"
      name="name"
      :class="{ 'is-invalid': failed }"
      placeholder="輸入姓名"
    />
    <span v-if="failed" class="text-danger">{{ errors[0] }}</span>
</ValidationProvider>
```
- `name` 為報錯時，顯示的名稱。
- 在 `rules` 上，添加要驗證的規則。
- 插槽範圍內可用的屬性，可以參考 [官方文件](https://logaretm.github.io/vee-validate/api/validation-provider.html#scoped-slot-props)。
  - `errors`：錯誤訊息列表，`errors[0]` 則代表驗證錯誤規則底下定義的 `messages`
  - `failed`：如果經過驗證且無效，則值為 `false`

預設情況下，ValidationProvider 渲染器為 `span` 標籤，可以使用套件提供的 `prop` 來改變呈現標籤：
```html
<ValidationProvider tag="div"></ValidationProvider>
```

#### 3.3 更詳細
更多可用 `prop` 可以參考 [官方文件](https://logaretm.github.io/vee-validate/api/validation-provider.html#props)。

另外還提供了許多方法可以使用，可以參考 [官方文件](https://logaretm.github.io/vee-validate/api/validation-provider.html#methods)。

### 4. 表單提交前的處理

```javascript
import { ValidationObserver } from 'vee-validate';

// 在 main.js 全域註冊元件
Vue.component('ValidationObserver', ValidationObserver);

// 區域註冊元件（推薦）
export default {
  components: {
    ValidationObserver,
  },
};
```

詳細的處理表單範例，可以參考 [官方文件](https://logaretm.github.io/vee-validate/guide/forms.html#basic-example)。

基本範例：
```html
<ValidationObserver v-slot="{ invalid }">
  <form @submit.prevent="onSubmit">
    <!--   ....   -->
    <button type="submit" :disabled="invalid">Submit</button>
  </form>
</ValidationObserver>
```
提交按鈕目前為禁用，只有當表單通過驗證才可以送出。

提交前驗證：
```html
<ValidationObserver v-slot="{ handleSubmit }">
  <form @submit.prevent="handleSubmit(onSubmit)">
    <!--   ....   -->
    <button type="submit">Submit</button>
</ValidationObserver>
```
ValidationObserver 提供了一個 `handleSubmit` 可用於保護表單提交的 `handleSubmit` 功能。

重置表單：
```html
<ValidationObserver v-slot="{ reset }">
  <form @reset.prevent="reset">
    <!--   ....   -->
    <button type="reset">Reset</button>
  </form>
</ValidationObserver>
```

#### 4.1 使用 $refs

先通過 `$refs` 找到 `ValidationObserver` 元件，呼叫組件中的方法 `validate()`，如果所有條件都為真（就是說所有的校驗都通過了）回傳 `true`，否則回傳 `false`。

```html
<ValidationObserver ref="form">
  <form @submit.prevent="onSubmit">
  
  </form>
</ValidationObserver>
```
```javascript
onSubmit () {
  this.$refs.form.validate().then((success) => {
    if (success) {
      // 證成功後的行為包含 AJAX傳送、重製表單等等
    } else {
      // 驗證失敗產生的行為
    }
  });
},
```

>[官方文件](https://logaretm.github.io/vee-validate/api/validation-observer.html#methods)。


#### 4.2 顯示初始驗證狀態

使用 `immediate`顯示　初始驗證狀態：
```html
<ValidationObserver rules="required"  v-slot="{ errors }"　immediate>
  <!-- ... -->
</ValidationObserver>
```

詳細的互動和用戶體驗範例，可以參考 [官方文件](https://logaretm.github.io/vee-validate/guide/interaction-and-ux.htm)。

#### 4.3 更詳細

插槽範圍內可用的屬性，可以參考 [官方文件](https://logaretm.github.io/vee-validate/api/validation-observer.html#scoped-slot-props)。

更多可用 `prop` 可以參考 [官方文件](https://logaretm.github.io/vee-validate/api/validation-observer.html#props)。


### 5. 驗證狀態

[驗證狀態](https://logaretm.github.io/vee-validate/guide/state.html#validation-state)


### 6. 中文化

`vee-validate` 附帶了一個很小的 i18n 字典，可滿足基本的 i18n 需求。`vee-validate` 的預設語言是 `en`。

雖然 `vee-validate` 具有 40 多個可用於互動的驗證的語言環境，但是預設情況下不會安裝它們，因此我們需要導入所需的語言環境。

導入安裝語言環境所需的 `localize` 函式：
```javascript
import { localize } from 'vee-validate';
```

安裝需要的使用語言環境，並使用 `localize()` 增加新的語言環境：
```javascript
import TW from 'vee-validate/dist/locale/zh_TW.json';

localize('zh_TW', TW);
```

