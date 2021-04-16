---
title: "CSS Grid"
date: 2021-01-02
description: "CSS Grid"
keywords: ["CSS Grid", "CSS排版","竹白"]
tags: ["CSS Grid", "CSS排版"]
categories: ["HTML/CSS 筆記"]
image: images/covers/2101/css_grid.png
draft: false
---

<!--more-->
{{< featuredImage >}}

## 前言

建構網頁佈局是製作網站的基礎，早期排版是許多開發者的惡夢，但隨著 Flex 如毒品般的出現，用過就回不去了。許多前端框架使用 Flex 實現 Grid system，例如 Bootstrap。但基於 Flex 的 Grid system 靈活度有時候還是不太夠，而且遇到雙向排版時，會使用大量的巢狀結構。

CSS Grid 的出現，可以說是 CSS 真正的 Grid system，為目前 CSS 唯一的二維佈局。

## 瀏覽器支援

主流瀏覽基本上都支持，詳細支援情況可以查閱 [**can I use**](https://caniuse.com/css-grid) 。

## 基本概念

![](https://i.imgur.com/NEhHkJX.png)

- 網格容器 Grid container，網格系統的外層框架。
- 網格項目 Grid item，網格容器下的直接子元素。
- 網格線 Grid line，組成網格的分割線。
- 網格軌道 Grid track，兩條網格線之間的區域，稱為網格欄 Grid column 或網格列 Grid row。
- 網格單元格 Grid cell，網格的最小的面積單位。
- 網格區塊 Grid area，由四條網格線所組成的矩形區域。

## 建立容器

使用 CSS Grid 的必要屬性。
```html
<div class="container"></div>
```
```css
.container {
  display: grid | inline-grid;
}
```
宣告以後，該元素會變成 grid container，所有直接子元素會變成 grid item。

## 畫格子

接下來我們來畫格子，也就是定義出基礎的 grid cell，我們可以使用 `grid-template-rows` 和 `grid-template-columns` 來放置網格內的 grid line，這樣就產生垂直與水平的 grid track，也就是欄與列，兩者交叉就會形成一格一格的 grid cell。
```css
.container {
  grid-template-rows: <track-size>...;
  grid-template-columns: <track-size>...;
  
  /* 簡寫 rows / columns */
  grid-template: <track-size>... / <track-size>...;
}
```
`track-size` 指的是 grid line 之間的距離，也就是 grid track 的尺寸。

舉例來說：
```css
.container {
  grid-template-rows: 120px 180px;
  grid-template-columns: 120px 120px 60px;
}
```
上面程式碼的效果如下，2 列 3 欄會形成 6 格 grid cell：

![](https://i.imgur.com/BGXQaRJ.png)

我們可以開啟 Chrome Devtool 查看：

![](https://i.imgur.com/OuuUkZE.png)

接著放入 grid item：
```css
.item {
  border-radius: 12px;
  border: 2px solid #222;
}
```
```html
<div class="container">
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
</div>
```
預設的情況下，grid item 會各佔一格 grid cell。

<iframe height="500" style="width: 100%;" scrolling="no" title="CSS Grid - 畫格子" src="https://codepen.io/CHUPAIWANG/embed/GRjOQYo?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/GRjOQYo'>CSS Grid - 畫格子</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 1. fr 單位

如果 grid container 有定義寬高：
```css
.container {
  width: 300px;
  height: 300px;
  display: grid;
}
```

我們可以使用 CSS Grid 專用單位 `fr`（fraction） 依照比例分配剩餘空間：
```css
.container {
  grid-template-rows: 100px 1fr;
  grid-template-columns: 1fr 3fr;
}
```

使用 `%` 需要手動計算，而 `fr` 只需要設置幾等份就好，非常方便。

<iframe height="500" style="width: 100%;" scrolling="no" title="CSS Grid -  fr 單位" src="https://codepen.io/CHUPAIWANG/embed/oNzepGQ?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/oNzepGQ'>CSS Grid -  fr 單位</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


### 2. repeat() 函式

若尺寸值相同，可以使用 `repeat()` 表示重複的值：
```css
.container {
  grid-template-rows: repeat(3, 1fr);
  /* 等同 */
  grid-template-rows: 1fr 1fr 1fr;
}
```

```css
.container {
  grid-template-rows: 10px repeat(3, 1fr) 10px;
  /* 等同 */
  grid-template-rows: 10px 1fr 1fr 1fr 10px;
}
```

```css
.container {
  grid-template-rows: repeat(3, 1fr 2fr);
  /* 等同 */
  grid-template-rows: 1fr 2fr 1fr 2fr 1fr 2fr;
}
```

```css
.container {
  grid-template-rows: repeat(2, 1fr) 10px repeat(2, 2fr);
  /* 等同 */
  grid-template-rows: 1fr 1fr 10px 2fr 2fr;
}
```

### 3. 最大最小值

如果我們的 grid container 會隨著視窗做變化，當你使用 `fr` 或 `auto` 作為 `track-size` 大小時，要避免 grid track 太大或太小，可以使用 `minmax()` 函式可以定義最小最大值，。

舉例來說，欄寬最小值 `auto`、最大值不超過 `150px`：
```css
.container {
  width: 100%;
  display: grid;
  grid-template-rows: 60px;
  grid-template-columns: minmax(auto, 150px) 100px 50px;
}
```

<iframe height="500" style="width: 100%;" scrolling="no" title="CSS Grid - minmax" src="https://codepen.io/CHUPAIWANG/embed/WNGMZLx?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/WNGMZLx'>CSS Grid - minmax</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 4. auto-fill、auto-fit

使用 `repeat()` 時，若不知道要重複的數量，可以用 `auto-fill` 或 `auto-fit` 自動將 grid track 填滿容器。

兩者差異在於，`auto-fit` 沒有用到的 grid track 會被設為 `0px`。

```html
<div class="container auto-fill"></div>

<div class="container auto-fit"></div>
```
```css
.container {
  background: #eee;
  width: 200px;
  display: grid;
  grid-template-rows: repeat(4, 50px);
}

.auto-fill {
  grid-template-columns: repeat(auto-fill, 50px);
}

.auto-fit {
  grid-template-columns: repeat(auto-fit, 50px);
}
```

![](https://i.imgur.com/cI9nk2V.png)

接下來我們放入 grid item，兩者基本上沒什麼差別。

<iframe height="500" style="width: 100%;" scrolling="no" title="CSS Grid - auto-fill、auto-fit" src="https://codepen.io/CHUPAIWANG/embed/yLapEpb?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/yLapEpb'>CSS Grid - auto-fill、auto-fit</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

但如果我們將欄的 `track-size` 改成 `minmax(50px, 1fr)` 就會有明顯的差異。

<iframe height="500" style="width: 100%;" scrolling="no" title="CSS Grid - auto-fill、auto-fit 差異" src="https://codepen.io/CHUPAIWANG/embed/mdrXvpv?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/mdrXvpv'>CSS Grid - auto-fill、auto-fit 差異</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


## 放置網格項目

預設的情況下，grid item 各佔一格 grid cell。不過我們能分別定義 grid item 要佔的空間，也就是 grid area。
```css
.item {
  grid-row-start: <name>;
  grid-row-end: <name>;
  
  grid-column-start: <name>;
  grid-column-end: <name>;

  /* 簡寫 start / end */
  grid-row: <name> / <name>;
  grid-column: <name> / <name>;
  
  /* 簡寫 row start / column start / row end / column end*/
  grid-area: <name> / <name> / <name> / <name>;
}
```
`start` 與 `end` 分別表示 **起始** 與 **結束** 的grid line。

Grid line 是有名稱的，預設為數字編號，由左至右（由上至下）為正值，由右至左（由下至上）則為負值。

![](https://i.imgur.com/AmFhtoX.png)

舉例來說：
```css
.item {
  grid-row-start: 2;
  grid-row-end: 5;
  grid-column-start: 2;
  grid-column-end: 4;
}
```

<iframe height="500" style="width: 100%;" scrolling="no" title="CSS Grid - 格線" src="https://codepen.io/CHUPAIWANG/embed/vYXeGrG?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/vYXeGrG'>CSS Grid - 格線</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 1. 跨度 span

除了指定 grid line 名稱，也可以使用 **跨度** `span <number>` 表示要跨幾格 grid cell。

舉例來說，從第 5 條水平線往上跨 2 格、從第 2 條垂直線往左跨 3 格：
```css
.item {
  grid-row-start: span 2;
  grid-row-end: 5;
  grid-column-start: 2;
  grid-column-end: span 3;
}
```

<iframe height="500" style="width: 100%;" scrolling="no" title="CSS Grid - 欄位數" src="https://codepen.io/CHUPAIWANG/embed/YzGrWXN?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/YzGrWXN'>CSS Grid - 欄位數</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 2. 自訂義名稱

除了預設編號，grid line 能使用自訂義名稱：
```css
.container {
  grid-template-rows: [name] <track-size> [name]...;
  grid-template-columns: [name] <track-size> [name]...;
  
  /* 簡寫 rows / columns */
  grid-template: [name] <track-size> [name]... / [name] <track-size> [name]...;
}
```

舉例來說：
```css
.container {
  grid-template-rows: [y1] 1fr [y2] 1fr [y3] 1fr [y4] 1fr [y5] 1fr [y6];
  grid-template-columns: [x1] 1fr [x2] 1fr [x3] 1fr [x4] 1fr [x5] 1fr [x6];
}

.item {
  grid-row: y1 / y4;
  grid-column: x1 / x5;
}
```
<iframe height="500" style="width: 100%;" scrolling="no" title="CSS Grid - 自訂義名稱" src="https://codepen.io/CHUPAIWANG/embed/wvzrEKZ?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/wvzrEKZ'>CSS Grid - 自訂義名稱</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 3. 順序

當我們手動放置 grid item 時，它們的 grid area 可能會有重疊的部分，若要改變顯示順序，可以使用 `z-index` 分層或 `order` 排序。

```css
.item-1 {
  background: rgba(255, 0, 0, 0.8);
  grid-row: 1/ 4;
  grid-column: 1 / 5;
  z-index: 2;
}

.item-2 {
  background: rgba(0, 255, 0, 0.8);
  grid-row: 3 / 5;
  grid-column: 2 / 6;
  z-index: 3;
}

.item-3 {
  background: rgba(0, 0, 255, 0.8);
  grid-row: 2 / 6;
  grid-column: 1 / 3;
  z-index: 1;
}
```

<iframe height="500" style="width: 100%;" scrolling="no" title="CSS Grid - 順序" src="https://codepen.io/CHUPAIWANG/embed/VwKyRXe?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/VwKyRXe'>CSS Grid - 自訂義名稱</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 4. auto

預設的情況下，grid item 各佔一格 grid cell，因為 `grid-area` 的預設值為 `auto`，grid item 會被以預設跨度 1 自動放置。

`auto` 會自動尋找下一條可用的 grid line，如果要將 grid item 自動跨 2 欄 grid item 不重疊，只需要設置 `grid-column-end`：
```css
.item {
  grid-column-start: auto: /* 預設值 auto */
  grid-column-end: span 2;
}
```


<iframe height="500" style="width: 100%;" scrolling="no" title="CSS Grid - auto" src="https://codepen.io/CHUPAIWANG/embed/QWKaPKz?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/QWKaPKz'>CSS Grid - auto</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 5. 區塊

我們還可以使用 `grid-template-areas` 定義 grid area，再利用 `grid-area` 對應到相對的名稱。

```css
.container {
  grid-template-areas: <string>;
}
```
每個字串都代表一列，使用要定義的 grid area 名稱圍成矩形，沒有要使用到的 grid cell 可以使用任何字元跳過。

舉例來說：
```css
.container {
  /* ... */
  grid-template-areas:
    'xx xx . .  .'
    'xx xx . yy yy'
    '.  .  . yy yy' 
    'zz zz . yy yy'
    'zz zz . .  .';
}

.item-1 {
  grid-area: x;
}
.item-2 {
  grid-area: y;
}
.item-3 {
  grid-area: z;
}
```

注意，定義 grid area 只能是矩形的範圍，非矩形為無效宣告。

<iframe height="500" style="width: 100%;" scrolling="no" title="CSS Grid - 命名" src="https://codepen.io/CHUPAIWANG/embed/qBaPNRN?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/qBaPNRN'>CSS Grid - 命名</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


## 間距

Grid area 之間的 **間距**（gutter）。

```css
.container {
  /* 標準 */
  row-gap: <line-size>;
  column-gap: <line-size>;
  /* 簡寫 row column */
  gap: line-size> | <line-size> <line-size>;


  /* 舊語法 */
  grid-row-gap: <line-size>;
  grid-column-gap: <line-size>;
  /* 簡寫 row column */
  grid-gap: <line-size> | <line-size> <line-size>;
}
```
`line-size` 只能使用長度值或百分比。

<iframe height="500" style="width: 100%;" scrolling="no" title="CSS Grid - 間距" src="https://codepen.io/CHUPAIWANG/embed/QWKqyoG?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/QWKqyoG'>CSS Grid - 間距</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## 對齊

### 1. content

當網格環境小於 grid content 時，可以使用以下屬性對齊。

`justify-content` 水平對齊、`align-content` 垂直對齊：
- `start`：靠左對齊。
- `end`：靠右對齊。
- `center`：靠中間對齊
- `stretch`：依照比例拉寬到滿版。
- `space-around`：在各格線間放入多餘的空白，包含開頭和結尾。
- `space-between`：只在各格線間放入多餘的空白。
- `space-evenly`：效果同 `space-around` 但開頭和結尾的空白較多。

`place-content` 是兩者的簡寫，`align-content` 為第一個值、`justify-content` 為第二值，若兩者相等，可以只設置一個值。

```css
.container {
  width: 300px;
  height: 300px;
  display: grid;
  grid-template-rows: 50px 50px 50px;
  grid-template-columns: 50px 50px 50px;
  
  justify-content: space-between;
  align-content: space-between;
  
}
```

<iframe height="600" style="width: 100%;" scrolling="no" title="CSS Grid - 定位模式 content" src="https://codepen.io/CHUPAIWANG/embed/BaLwjGN?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/BaLwjGN'>CSS Grid - 定位模式 content</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 2. items

預設的情況下，我們的 grid item 大小會等於 grid area。但如果 grid item 有定義尺寸，小於 grid area 時，可以使用以下屬性對齊。

`justify-items` 水平對齊、`align-items` 垂直對齊：
- `start`：靠左對齊。
- `end`：靠右對齊。
- `center`：靠中間對齊
- `stretch`：依照比例拉寬到滿版。

`place-items` 則是兩者的簡寫，`align-items` 為第一個值、`justify-items` 為第二值，若兩者相等，可以只設置一個值。

```css
.container {
  width: 300px;
  height: 300px;
  display: grid;
  grid-template-rows: 100px 100px 100px;
  grid-template-columns: 100px 100px 100px;
  
  justify-items: center;
  align-items: center;
}

.item {
  width: 50px;
  height: 50px;
}
```

<iframe height="550" style="width: 100%;" scrolling="no" title="CSS Grid - 定位模式 items" src="https://codepen.io/CHUPAIWANG/embed/vYXWNem?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/vYXWNem'>CSS Grid - 定位模式 items</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 3. self

如果你的 grid item 要單獨對齊，可以在 grid item 中使用以下屬性對齊。

`justify-self` 水平對齊、`align-self` 垂直對齊：
- `start`：靠左對齊。
- `end`：靠右對齊。
- `center`：靠中間對齊
- `stretch`：依照比例拉寬到滿版。

`place-self` 則是兩者的簡寫，`align-self` 為第一個值、`justify-self` 為第二值，若兩者相等，可以只設置一個值。


## 隱式網格

在 grid cell 有限的情況下，如果 grid item 過多，就會自動增加 grid track，也就是 **隱式網格**。

### 1. 隱式網格的大小

隱式網格的 grid track 預測為自動大小，但可以透過 `grid-auto-rows` 和 `grid-auto-columns` 定義。

```css
.container{
  grid-auto-rows: <track-size> ...;
  grid-auto-columns: <track-size> ...;
}
```

舉例來說，我們只定義 2 欄 1 列：
```css
.container {
  grid-template-rows: 60px;
  grid-template-columns: repeat(2, 60px);

  grid-auto-rows: 120px;
}
```

如果我們放置 4 個 grid track，會自動新增第 2 列 grid track。

<iframe height="400" style="width: 100%;" scrolling="no" title="CSS Grid - 隱式" src="https://codepen.io/CHUPAIWANG/embed/MWjQpwx?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/MWjQpwx'>CSS Grid - 隱式</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## 網格流向

預設的情況下，網格流向為 row，也就是 grid item 自動放置時，會水平往右排列。

我們可以使用 `grid-auto-flow` 改變網格的流向：
```css
.container {
  grid-auto-flow: row | column | row dense | column dense;
}
```

舉例來說，將流向改成 column 垂直向下：
```css
.container {
  display: grid;
  grid-template-rows: repeat(3, 60px);
  grid-template-columns: repeat(3, 60px);
  grid-auto-flow: column;
}
```

<iframe height="500" style="width: 100%;" scrolling="no" title="CSS Grid - 流向" src="https://codepen.io/CHUPAIWANG/embed/qBaxXvj?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/qBaxXvj'>CSS Grid - 流向</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

若加上 `dense`，會嘗試將 grid track 填滿，因此可能會改變 grid item 順序，要特別留意。

```html
<div class="container">
  <div class="item item-2"></div>
  <div class="item item-2"></div>
  <div class="item item-1"></div>
  <div class="item item-1"></div>
</div>
```
```css
.container {
  display: grid;
  grid-template-rows: repeat(3, 60px);
  grid-template-columns: repeat(3, 60px);
  grid-auto-flow: row dense;
}

.item-1 {
  grid-column-end: span 1;
}

.item-2 {
  grid-column-end: span 2;
}
```

預設的情況下，第 1 列會有空隙，但使用 `row dense` 後，會將後面較小的 `item-1` 往前填滿。

<iframe height="500" style="width: 100%;" scrolling="no" title="CSS Grid - 流向 dense" src="https://codepen.io/CHUPAIWANG/embed/GRjQvaB?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/GRjQvaB'>CSS Grid - 流向 dense</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


## 佈局範例

### 1. 聖杯佈局

```html
<div class="container">
  <header>Header</header>
  <aside>Aside</aside>
  <main>Main</main>
  <aside>Aside</aside>
  <footer>Footer</footer>
</div>
```
```css
.container {
  width: 100%;
  min-width: 600px;
  display: grid;
  grid-template-columns: 100px auto 100px;
  gap: 10px;
}

header, footer {
  grid-column: 1 / 4;
}
```
<iframe height="600" style="width: 100%;" scrolling="no" title="CSS Grid - 聖杯佈局" src="https://codepen.io/CHUPAIWANG/embed/bGwLZrg?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/bGwLZrg'>CSS Grid - 聖杯佈局</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

移動端響應式佈局，可以加上媒體查詢：
```css
@media all and (max-width: 700px) {
  aside, main {
    grid-column: 1 / 4;
  }
}
```

<iframe height="500" style="width: 100%;" scrolling="no" title="CSS Grid - 聖杯佈局 響應式" src="https://codepen.io/CHUPAIWANG/embed/KKgQEro?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/KKgQEro'>CSS Grid - 聖杯佈局 響應式</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


### 2. 960 grid system

<iframe height="500" style="width: 100%;" scrolling="no" title="CSS Grid - 960 grid system" src="https://codepen.io/CHUPAIWANG/embed/qBaxGvy?height=265&theme-id=light&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/CHUPAIWANG/pen/qBaxGvy'>CSS Grid - 960 grid system</a> by CHUPAIWANG
  (<a href='https://codepen.io/CHUPAIWANG'>@CHUPAIWANG</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>