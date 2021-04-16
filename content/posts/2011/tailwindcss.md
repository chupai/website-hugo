---
title: "Vue CLI 安裝 TailwindCSS v2.0"
date: 2020-11-28
description: "Vue CLI 安裝 Tailwind CSS v2.0。"
keywords: ["Vue", "Tailwind CSS v2.0"]
tags: ["Vue", "TailwindCSS"]
categories: ["TailwindCSS"]
image: images/covers/2011/tailwindcss.png
draft: false
---

Vue CLI 安裝 Tailwind CSS v2.0。

<!--more-->

{{< featuredImage >}}

## 簡介

>[Tailwind CSS](https://tailwindcss.com/)

Tailwind CSS 是一個 Utility CSS 框架。

如果你有用過 Bootstrap，一定有用過它的通用類別，而 Tailwind 就是 100% 的通用類別不含 UI 元件，你可以更加輕鬆的客製化自己的元件。

- Tailwind 的實際案例，可以參考 [**Built with Tailwind**](https://builtwithtailwind.com/)。
- 更多關於 Tailwind 的資源，可以參考 [**Awesome Tailwind CSS**](https://github.com/aniftyco/awesome-tailwindcss)。

2020.11.19 Tailwind 升級到了 v2.0，詳細的更新內容可以參考 [**Tailwind CSS v2.0**](https://blog.tailwindcss.com/tailwindcss-v2)。

## 安裝

Tailwind 是 PostCSS 的套件，但不一定要使用 PostCSS 作為預處理器才能使用 Tailwind。如果只是簡單的專案或是體驗 Tailwind，可以使用 Tailwind CLI 來編譯 Tailwind CSS。

詳細的安裝流程，這裡就不多加說明，可以參考 [**官方文件**](https://tailwindcss.com/docs/installation)。

## Vue CLI

Vue CLI 內部本來就使用了 PostCSS 預設開啟 Autoprefixer，你只要安裝 Tailwind 和新增 `postcss.config.js`、`tailwind.config.js` 設定檔就可以使用了。

但是 v2.0 版本的 Tailwind 依賴於 PostCSS 8。而 Vue CLI 目前使用的是 PostCSS 7，因此執行後會現下列錯誤訊息：

{{< notice error >}}
Error: PostCSS plugin tailwindcss requires PostCSS 8.
{{< /notice >}}

解決辦法就是，重新安裝官方提供的 [PostCSS 7 兼容版本](https://tailwindcss.com/docs/installation#post-css-7-compatibility-build)：
```
npm uninstall tailwindcss
npm install tailwindcss@npm:@tailwindcss/postcss7-compat
```
你若是還有安裝使用其他 PostCSS 套件，最新版已經支援 PostCSS 8，記得降版本。



