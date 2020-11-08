---
title: "AJAX"
date: 2020-08-20
description: "AJAX 向伺服器發送 HTTP 請求的代名詞。"
keywords: ["JavaScript", "非同步", "AJAX" ,"竹白"]
tags: ["JavaScript", "非同步", "AJAX"]
categories: ["JavaScript筆記"]
image: images/covers/2008/js_ajax.png
draft: false
---

>[JavaScript筆記 目錄](/posts/190620_javascript)

AJAX：
- [AJAX](/posts/2008/js_ajax)
- [JSON](/posts/2008/js_json)
- [XMLHttpRequest](/posts/2008/js_xhr)
- [Fetch](/posts/2008/js_fetch)

<!--more-->

{{< featuredImage >}}

## 什麼是 AJAX？

AJAX 是 Asynchronous JavaScript and XML 的縮寫，中文名稱為「非同步 JavaScript 及 XML」，這個名詞出自 <a href="https://web.archive.org/web/20061107032631/http://www.adaptivepath.com/publications/essays/archives/000385.php" target="_blank">《Ajax: A New Approach to Web Applications》</a>。

原意指的是透過 JavaScript 的非同步通訊，從服務器獲取 XML 從中提取資料，再更新當前網頁的對應部分，而不用刷新整個網頁。

但後來，AJAX 這詞成為了 JavaScript 向伺服器發送 HTTP 請求的代名詞。不是指一種單一的技術，而是一種多技術的組合，其內容包含的技術有 HTML 或 XHTML、層疊樣式表、JavaScript、文件物件模型、XML、XSLT 以及最重要的 XMLHttpRequest 物件等等。當這些技術被結合在 AJAX 模型中，Web 應用程式便能快速、即時更動介面及內容，不需要重新讀取整個網頁，讓程式更快回應使用者的操作。

雖然 X 在 AJAX 中代表 XML，但後來 JSON 格式是最為流行的資料格式。

## 傳統網頁載入

傳統網頁載入資料與 AJAX 比較：
- **傳統的⽅式**
是以同步方式傳送請求、等待伺服器回應，在這段期間使用者只能發呆，當資料回應後，會重新載入整個網頁。
- **AJAX**
可與伺服器進行非同步更新，不需要等待伺服器回應依然可以進行其他操作，也不需要重新載入整個網頁，只更新要更新的部分。

## 使用 AJAX 的好處

- **提高可操作性**
  - 避免每次傳遞資料造成畫面更新閃動；
  - 伺服器端處理時，使用者仍可操作。
- **提升效能**
  - 只需要更新部分頁面，傳遞資料量可以控制在最小；
  - 不需要等待伺服器處理結束，提升了操作的流暢度。
- **提升開發產能與應用**
  - 只需要用瀏覽器標準技術，就可以架構使用者介面；
  - 不需要特別的外掛。

## AJAX 與 XMLHttpRequest

AJAX 技術在瀏覽器上，會利用 JavaScript（XMLHttpRequest 物件）與伺服器進行非同步傳遞，並將結果由 DOM 反映至頁面上。

流程：
1. 建立一個 XMLHttpRequest 物件；
2. 定義伺服器傳遞資料的處理流程；
3. 開始非同步處理。

但經過長久的時間，它在使用上產生不少問題，例如：
- XMLHttpRequest 實體肩負著太多任務，例如，請求設置、傳送、回應狀態判斷、事件監聽等等；
- 每次請求都要新建 XMLHttpRequest 實體，但請求同常通常只會做一次；
- 由於使用事件監聽獲取回應資料，因此會產生回呼地域的問題。

所以現在很少人會直接使用原生 XMLHttpRequest 物件來處理 AJAX，通常都會用以下方法來實作：
- 使用 jQuery 的 ajax 語法（原生 XHR 的封裝）
- AXIOS（原生 XHR 的封裝，使用 Promise）
- Fetch API（HTML5 的 API，使用 Promise）

除了 Fetch API，其他都屬於函式庫，需額外載入。

## 什麼是 CORS？

跨來源資源共用 （Cross-Origin Resource Sharing，CORS）是一種使用額外 HTTP 標頭令目前瀏覽網站的使用者代理取得存取其他來源（網域）伺服器特定資源權限的機制。當使用者代理請求一個不是目前文件來源，例如來自於不同網域（domain）、通訊協定（protocol）或通訊埠（port）的資源時，會建立一個跨來源 HTTP 請求（cross-origin HTTP request）。

基於安全性考量，程式碼所發出的跨來源 HTTP 請求會受到限制。例如，XMLHttpRequest 及 Fetch 都遵守同源政策（same-origin policy）。這代表網路應用程式所使用的 API 除非使用 CORS 標頭，否則只能請求與應用程式相同網域的 HTTP 資源。

如果要測試 API 是否開源 可以用 <a href="https://test-cors.org/" target="_blank">test-cors.org</a> 這個網站。

更多 CORS 詳細內容，可以參考 <a href="https://developer.mozilla.org/zh-TW/docs/Web/HTTP/CORS" target="_blank">CORS 跨來源資源共用｜MDN</a>。