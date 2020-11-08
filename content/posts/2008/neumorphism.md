---
title: "Neumorphism（Soft UI）"
date: 2020-08-31
description: "Neumorphism 是由「New Skeuomorphism」組合的新名詞，也就是新型態的擬物化風格設計。"
keywords: ["UI/UX", "Neumorphism", "Soft UI", "竹白"]
tags: ["UI/UX", "Neumorphism", "Soft UI"]
series: ["JavaScript 學演算法"]
categories: ["w3HexSchool六角鼠年鐵人賽", "UI/UX"]
image: images/covers/2008/neumorphism.png
draft: false
---

這週是六角鼠年鐵人賽第三十一週。這週原本是要說明分治法，但文章還沒整理好😅。

<!--more-->

{{< featuredImage >}}

## 什麼是 Neumorphism

**Neumorphism** 是由「New Skeuomorphism」組合的新名詞，也就是新型態的擬物化風格設計，中文稱作**新擬態**、**同化**，也有設計師稱其為 **Soft UI（軟UI）**。

Neumorphism 是 2020年最熱門的新風格，它透過燈光的原理，替圖形加上高光和陰影，類似於浮雕的效果，賦予元件真實和立體感。

此風格源頭為烏克蘭的設計師 Alexander Plyuto，於 2019年底所發表的作品[「Skeuomorph Mobile Banking」](https://dribbble.com/shots/7994421-Skeuomorph-Mobile-Banking)。

![Skeuomorph Mobile Banking](https://i.imgur.com/ErJDgyZ.jpg)

## 設計風格發展

### 1. Skeuomorphism（擬物）

Skeuomorphism 風格最早的起源是 2007年 Apple 發布 iOS 1。

![](https://i.imgur.com/LUAKQjg.png)

Skeuomorphism 最大特徵就是模擬真實物件的外觀，那個時期幾乎所有 UI 都是 Skeuomorphism。

### 2. Flat Design（扁平設計）

接下來，2010 年微軟發布他們首款智慧型手機 Window Phone 中，可以說是首款應用 Flat Design 風格的智慧型裝置。
![](https://i.imgur.com/rF0Wgce.png)

Flat Design 最大特徵就是去除陰影、漸層、裝飾、反射光澤等等的模擬 3D 要素，呈現出簡潔的風格。

直到 2013年 Apple iOS7 捨棄了 Skeuomorphism，也改用更簡潔的 Flat Design。

![](https://i.imgur.com/iH0yJ1z.png)

從 2014 年開始 Flat Design 風格的 UI 設計開始大流行，形成新一代介面設計的熱潮，不僅僅是 UI 介面，連許多大企業的 LOGO 也都捨棄 Skeuomorphism 改成 Flat Design 風格。

最有名的例子 Instagram：
![](https://i.imgur.com/eSk4IfV.png)

### 3. Material Design（卡片式設計、實感設計）

但 Flat Design 風格的最大問題就是容易造成審美疲勞，所有資訊沒有分層，直到 2017年 Google 推出了 Material Design，將現實世界中紙張的特性用在虛擬中，利用陰影模擬紙張空間。

![](https://i.imgur.com/rx5Mpn2.png)

目前主流 UI 設計幾乎是 Flat Design 和 Material Design。

## Neumorphism 特徵

Neumorphism 此風格同時擁有高光和陰影。

具有以下幾點特徵：
1. 元件不漂浮；
2. 元件顏色與背景統一，不能純黑或純白；
3. 利用明的暗陰影產生凹凸感。

扁平、實感、擬物化風格：
<iframe height="265" style="width: 100%;" scrolling="no" title="Neumorphism DEMO" src="https://codepen.io/chupai/embed/ExKmzVy?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/chupai/pen/ExKmzVy'>Neumorphism DEMO</a> by Chupai@Design
  (<a href='https://codepen.io/chupai'>@chupai</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## CSS 樣式

### 1. 凸

```javascript
.circle {
  width: 200px;
  height: 200px;
  border-radius: 50%;
  background: #E6EEF8;
  box-shadow: 
  -6px -6px 16px rgba(255, 255, 255, 0.8),
   6px  6px 16px rgba(0, 0, 0, 0.2);
}
```
<iframe height="400" style="width: 100%;" scrolling="no" title="Neumorphism Concave" src="https://codepen.io/chupai/embed/QWNgOLj?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/chupai/pen/QWNgOLj'>Neumorphism Concave</a> by Chupai@Design
  (<a href='https://codepen.io/chupai'>@chupai</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 2. 凹

```css
.circle {
  width: 200px;
  height: 200px;
  border-radius: 50%;
  background: #E6EEF8;
  box-shadow: 
  inset -6px -6px 16px rgba(255, 255, 255, 0.8),
  inset 6px 6px 16px rgba(0, 0, 0, 0.2);
}
```

<iframe height="400" style="width: 100%;" scrolling="no" title="Neumorphism Convex" src="https://codepen.io/chupai/embed/KKzqyMg?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/chupai/pen/KKzqyMg'>Neumorphism Convex</a> by Chupai@Design
  (<a href='https://codepen.io/chupai'>@chupai</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 3. 疊加

<iframe height="400" style="width: 100%;" scrolling="no" title="Neumorphism 疊加" src="https://codepen.io/chupai/embed/MWyoOEv?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/chupai/pen/MWyoOEv'>Neumorphism 疊加</a> by Chupai@Design
  (<a href='https://codepen.io/chupai'>@chupai</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 4. 總結

分辨光源位置製造凹凸有致的效果，最基本的光源角度為 45度角，也就左上角和右下角，也可以用其他角度。

背景也可以使用漸層稍作一點點變化，讓元件表面看起來不是平面的，陰影模糊程度可以視想要呈現的材質來決定，較大看起來較柔軟。

>[Neumorphism.io](https://neumorphism.io/)
線上工具

## 範例

<iframe height="265" style="width: 100%;" scrolling="no" title="Neumorphism Text Effect" src="https://codepen.io/chupai/embed/qBZjVQg?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/chupai/pen/qBZjVQg'>Neumorphism Text Effect</a> by Chupai@Design
  (<a href='https://codepen.io/chupai'>@chupai</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


<iframe height="350" style="width: 100%;" scrolling="no" title="Neumorphism UI - Social" src="https://codepen.io/chupai/embed/Jjogomv?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/chupai/pen/Jjogomv'>Neumorphism UI - Social</a> by Chupai@Design
  (<a href='https://codepen.io/chupai'>@chupai</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

<iframe height="420" style="width: 100%;" scrolling="no" title="Neumorphism - block" src="https://codepen.io/chupai/embed/PoNjXpW?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/chupai/pen/PoNjXpW'>Neumorphism - block</a> by Chupai@Design
  (<a href='https://codepen.io/chupai'>@chupai</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

<iframe height="600" style="width: 100%;" scrolling="no" title="Neumorphism UI - Login Form" src="https://codepen.io/chupai/embed/yLymJZm?height=265&theme-id=dark&default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/chupai/pen/yLymJZm'>Neumorphism UI - Login Form</a> by Chupai@Design
  (<a href='https://codepen.io/chupai'>@chupai</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## 總結

在主流 UI 設計幾乎是 Flat Design 風格的情況下，Neumorphism 帶給人新穎、新鮮、未來感，但說實在的，此風格對於視力較差的使用者來說，可辨識非常不友善。因為它的元件顏色與背景相同，基本上是依賴高光和陰影凸顯出元件本身，舉例來說，你的螢幕對比稍微不準確一點，元件會直接與背景融合。

以上是我的看法，但我不設計師，因此若對此風格有更多興趣，可以參考以下文章：
- [Neumorphism（新拟态）UI设计趋势吗？](https://www.zcool.com.cn/article/ZMTA5ODAwNA==.html)
- [新擬物化設計 Neumorphism 讓 UIUX 設計師重新思考的 5 件事](https://medium.com/as-a-product-designer/新擬物化設計neumorphism讓uiux設計師重新思考的5件事-9c0eff8dcd90)
- [用户界面中的Neumorphism（新拟物风格）设计](http://www.woshipm.com/pd/3425576.html)
- [为什么2020年初爆火的新拟物化设计，完全无法落地使用？](https://www.uisdc.com/neumorphism-ui)
- [“热火朝天”的新拟物设计趋势，你需要理性看待](https://zhuanlan.zhihu.com/p/144429072)
- [趋势 I 解析新拟态风格最全指南](https://www.zcool.com.cn/article/ZMTEwMjQ3Ng==.html)
- [新擬態——國外設計師分析的全新UI趨勢](http://www.woshipm.com/pd/3386232.html)

另外，最近找到一個基於 Bootstrap 的 [Neumorphism UI](https://demo.themesberg.com/neumorphism-ui/)，可以玩玩看。