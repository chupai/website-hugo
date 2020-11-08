---
title: "表單元素 - 下拉選單"
date: 2020-09-03
description: "表單元素的下拉選單是由 `<select>` 和 `<option>` 組成的。"
keywords: ["HTML/CSS 筆記", "表單元素", "竹白"]
tags: ["HTML/CSS 筆記", "下拉選單", "表單元素"]
categories: ["HTML/CSS 筆記"]
image: images/covers/htmlcss/select.png
draft: true
---

>[HTML/CSS 筆記 - 目錄](/posts/2009/htmlcss_menu/)

<!--more-->
{{< featuredImage >}}

## 下拉選單

表單元素的下拉選單是由 `<select>` 和 `<option>` 組成的。

一個基本的下拉選單：
```html
<select>
  <option value="A">A</option>
  <option value="B">B</option>
  <option value="C">C</option>
</select>
```
`<select>` 為選單容器，`<option>` 則是選項。

<iframe height="265" style="width: 100%;" scrolling="no" title="select - Base demo" src="https://codepen.io/CHUPAIWANG/embed/XWdeeXw?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/XWdeeXw'>select - Base demo</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## select

### 1. 顯示的選項數

`size` 屬性可以設置要顯示的選項數：
```html
<select size="4">
  <option value="A">A</option>
  <option value="B">B</option>
  <option value="C">C</option>
  ...
  <option value="F">F</option>
</select>
```
<iframe height="265" style="width: 100%;" scrolling="no" title="select - size" src="https://codepen.io/CHUPAIWANG/embed/zYqEpJM?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/zYqEpJM'>select - size</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 2. 多選項

使用 `multiple` 屬性可以複數選取選項。

```html
<select multiple>
  <option value="A">A</option>
  <option value="B">B</option>
  <option value="C">C</option>
  <option value="D">D</option>
  <option value="E">E</option>
  <option value="F">F</option>
</select>
```
需搭配 Ctrl 鍵。
<iframe height="265" style="width: 100%;" scrolling="no" title="select - multiple" src="https://codepen.io/CHUPAIWANG/embed/YzqreYZ?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/YzqreYZ'>select - multiple</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 3. 其他屬性

- `autofocus`：網頁載入時，下拉選單自動獲得焦點。
- `required`：提交表單時，必須選擇下拉選單的選項。
- `form`：若 `<select>` 在 `<form>` 外，可用來設定所屬表單。


## option

### 1. 預設選項

若要指定下拉選單的預設選項，可以使用 `selected` 屬性，代表被選取狀態。

```html
<select>
  <option value="A">A</option>
  <option value="B">B</option>
  <option value="C" selected>C</option>
</select>
```
<iframe height="265" style="width: 100%;" scrolling="no" title="select - selected" src="https://codepen.io/CHUPAIWANG/embed/XWdeVRY?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/XWdeVRY'>select - selected</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 2. 下拉選單標題

通常會將下拉選單的第一個選項設為標題，這裡會搭配 `disabled` 屬性。

```html
<select>
  <option disabled>--請選擇--</option>
  <option value="A">A</option>
  <option value="B">B</option>
  <option value="C">C</option>
</select>
```

<iframe height="265" style="width: 100%;" scrolling="no" title="select - disabled" src="https://codepen.io/CHUPAIWANG/embed/VwaMyzJ?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/VwaMyzJ'>select - disabled</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 3. 預設標題

若不希望預設選項出現在選單中，而是設定標題，可以使用 `hidden` 屬性搭配 `selected`、`disabled` 屬性。
```html
<select>
  <option selected disabled hidden>--請選擇--</option>
  <option value="A">A</option>
  <option value="B">B</option>
  <option value="C">C</option>
</select>
```
<iframe height="265" style="width: 100%;" scrolling="no" title="select - hidden" src="https://codepen.io/CHUPAIWANG/embed/bGpoavq?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/bGpoavq'>select - hidden</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


## 群組選項

`<select>` 內還有一個 `<optgroup>` 可以使用，它可以用來建立選項的群組。

`label` 屬性表示群組標題：
```html
<select>
  <optgroup label="abc">
    <option value="A">A</option>
    <option value="B">B</option>
    <option value="C">C</option>
  </optgroup>
  <optgroup label="number">
    <option value="1">1</option>
    <option value="2">2</option>
    <option value="3">3</option>
  </optgroup>
</select>
```
<iframe height="265" style="width: 100%;" scrolling="no" title="optgroup" src="https://codepen.io/CHUPAIWANG/embed/VwaMQOz?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/VwaMQOz'>optgroup</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## 美化選單

簡易樣式修改：
```html
<div class="custom-select">
  <select>
    <option selected disabled hidden>Dropdown</option>
    <option value="html">HTML</option>
    <option value="css">CSS</option>
    <option value="javascript">JavaScript</option>
    <option value="vue">Vue</option>
    <option value="react">React</option>
    <option value="angular">Angular</option>
  </select>
</div>
```
```css
.custom-select {
  position: relative;
  margin-bottom: 5px;
}

/* 箭頭 */
.custom-select::after {
  position: absolute;
  right: 0;
  content: '▼';
  width: 40px;
  height: 40px;
  line-height: 40px;
  text-align: center;
  color: black;
  background: rgba(0, 0, 0, 0.05);
  pointer-events: none; /* 穿透 避免擋到 select */
}

.custom-select select {
  /* 取消預設樣式 */
  appearance: none;
  -moz-appearance: none;
  -webkit-appearance: none;
  
  border: none;
  outline: none;
  background: #48cae4;

  padding: 10px;
  padding-right: 40px;
  width: 200px;
  height: 40px;
}

/* disabled */
.custom-select select[disabled] {
  color: rgba(0, 0, 0, 0.5);
  background: rgba(0, 0, 0, 0.2);
}
```
<iframe height="265" style="width: 100%;" scrolling="no" title="dropdown" src="https://codepen.io/CHUPAIWANG/embed/jOqGZgo?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/jOqGZgo'>dropdown</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

我們只能透過 CSS 稍微修改 `<select>` 樣式，但是 `<option>` 的樣式受到保護，通常無法做進一步的修改。

若要美化下拉選單，會改用 `<ul>` + `<li>` + `<input type="checkbox">` 搭配 JavaScript 來實現。

