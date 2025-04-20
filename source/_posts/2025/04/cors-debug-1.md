---
title: CORS Debug (1) - 網頁快取導致的 CORS 問題
date: 2025-04-13 22:41:49
categories: Javascript
tags:
---

###### 此為 **CORS Debug** 系列文章 - 第 1 篇：

1. <a href="/blog/2025/04/13/cors-debug-1-網頁快取導致的-cors-問題/" target="_blank">CORS Debug (1) - 網頁快取導致的 CORS 問題</a>
2. <a href="/blog/2025/04/17/cors-debug-2-src-與-crossorigin-在-img-元素中的順序導致-cors-error/" target="_blank">CORS Debug (2) - src 與 crossOrigin 在 img 元素中的順序導致 CORS error</a>

## 簡介

最近做到一個在網頁上展示圖片的功能，簡單來說就是點擊右側的圖片庫，左邊的主畫面可以展示大圖，如下圖所示，這是一個非常簡單的功能，但到最後發現這跟 [CORS](https://developer.mozilla.org/zh-TW/docs/Web/HTTP/Guides/CORS) 有密切的關係，下面我們來一步步探討看看

<div style="display: flex; justify-content: center;">
  <img src="/blog/2025/04/13/cors-debug-1-網頁快取導致的-cors-問題/demo.png" />
</div>

<!--more-->

## 需求 - 下載圖片功能

除了展示圖片之外，還需要能夠個別下載圖片的功能，點擊圖片庫右上角的 **下載** 可以將圖片載下來，下載圖片的程式碼如下：

```js
const downloadImage = async (image: Image) => {
  try {
    const response = await fetch(image.url);
    const blob = await response.blob();
    const urlObject = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = urlObject;
    a.download = image.filename;
    document.body.appendChild(a);
    a.click();
    document.body.removeChild(a);
    URL.revokeObjectURL(urlObject);
  } catch (error) {
    window.alert(`下載失敗: ${error.message}`);
  }
};
```

## 問題 - 無法下載圖片

當點擊下載按鈕後，登愣！圖片竟然無法下載！

<div style="display: flex; justify-content: center;">
  <img src="./download-fail.png" />
</div>

<div style="display: flex; justify-content: center;">
  <img src="./download-cors-error.png" />
</div>

這時打開 DevTools 中的 console 可以看到下載圖片失敗的原因是 CORS 錯誤

=> 為什麼會出現 CORS 錯誤呢？這其實跟圖片資源所在的後端伺服器息息相關，就我遇到的狀況來說，這個網頁顯示的圖片都儲存在 Amazon S3 上，而取得 Amazon S3 裡的資源時需要遵守他們的 **CORS** 規範

#### 1. Amazon S3 上的 CORS 規範

當請求 Amazon S3 中的資源時，基本上分為兩種獲取方式，一個是 **非跨域請求**、另一個是 **跨域請求** 也就是所謂的 **CORS**

##### - 非跨域請求

當 `img` 元素單純只有 `src` 的網址時，會對 Amazon S3 中的圖片發出 **非跨域請求**

```html
<img
  src="https://rjzdhlvuuafqvgoyticm.supabase.co/functions/v1/getCORSImage?image=building.jpg"
  alt="building"
/>
```

##### - 跨域請求 (CORS)

當 `img` 元素額外加上 [crossorigin](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Reference/Attributes/crossorigin) 的屬性時，會對 Amazon S3 中的圖片發出 **跨域請求**

```html
<img
  src="https://rjzdhlvuuafqvgoyticm.supabase.co/functions/v1/getCORSImage?image=building.jpg"
  crossorigin="anonymous"
  alt="building"
/>
```

當瀏覽器發出 **跨域請求** 的時候 [會在 Request headers 中額外加上 Origin 的資訊](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Reference/Headers/Origin#%E6%8F%8F%E8%BF%B0)，讓資源所在的伺服器知道是哪個網站在請求這項資源

<div style="display: flex; justify-content: center;">
  <img src="./request-origin.png" />
</div>

在 [S3 - Access-Control-Allow-Origin Header](https://stackoverflow.com/a/32887912/10090927) 的討論文中提到：

> If your request doesn't specify an "Origin" header, S3 won't include the CORS headers in the response. This really threw me because I kept trying to curl the files to test the CORS but curl doesn't include Origin.

這句話指的是當 Request headers 帶有 "Origin" 欄位時，Amazon S3 回覆的 Response headers 才會多增加 CORS header 相關的欄位，CORS header 相關的欄位大致上有三個

- `Access-Control-Allow-Headers`
- `Access-Control-Allow-Methods`
- `Access-Control-Allow-Origin`

其中的 `Access-Control-Allow-Origin` 需要回傳請求資源網站符合的網址，才代表 S3 認同你這個網站通過 CORS 規範，有權限取得 S3 裡面的資源

所以當我們在 DevTools 中查看 Response headers 中有 `Access-Control-Allow-Origin: *` 的欄位時，代表著這張圖片是以 **跨域請求 (CORS)** 的方式獲得的

<div style="display: flex; justify-content: center;">
  <img src="./origin-star.png" />
</div>

P.S. 在這篇文章中，`img.src` 圖片資源的網址都是 `https://rjzdhlvuuafqvgoyticm.supabase.co/` 開頭的，實際上我遇到無法下載圖片問題的時候後端伺服器是 Amazon S3，但這裡改用 [supabase](https://supabase.com/) 模擬 Amazon S3 Response headers 回傳的行為，主要是考慮到這樣可以更靈活的根據前端傳遞的 url，設置不同的 Response headers 來做測試

#### 2. Amazon S3 上的快取機制 (cache)

圖片下載失敗的原因之一除了 Amazon S3 上的 **CORS** 規範外，另外一個重點是 [網頁的快取機制(cache)](https://developer.mozilla.org/zh-TW/docs/Web/HTTP/Guides/Caching)

在這個範例中，我在後端的伺服器設定了圖片的快取為 `Cache-Control: public max-age=30`，這代表當從伺服器獲取圖片後的 30s 內，如果再度對圖片進行請求都會從瀏覽器的快取中拿，而不會真正發出網路請求到伺服器

##### - 第一次請求

第一次請求時直接從伺服器取得圖片，而且由於 Response headers 設置了 `Cache-Control: public max-age=30`，所以後續 30s 內對同一張圖片的請求都會從瀏覽器的快取中取得

<div style="display: flex; justify-content: center;">
  <img src="./cache-control-30s.png" />
</div>

##### - 後續請求

在 30s 內對同一張圖片的後續請求，由於快取尚未過期，所以可以看到圖片來源自 **瀏覽器本身的快取(memory cache)**

<div style="display: flex; justify-content: center;">
  <img src="./memory-cache.png" />
</div>

#### 小結 - 為什麼下載圖片觸發了 CORS 錯誤？

最初畫面上載入圖片時的寫法是這樣的，這種寫法是用 **非跨域請求** 的方式取得 Amazon S3 上的圖片

```html
<img
  src="https://rjzdhlvuuafqvgoyticm.supabase.co/functions/v1/getCORSImage?image=building.jpg"
  alt="building"
/>
```

##### 使用 fetch 獲取 CORS 的資源

在 [跨來源資源共享（CORS）](https://developer.mozilla.org/zh-TW/docs/Web/HTTP/Guides/CORS)的頁面敘述如下：

> 出於安全原因，瀏覽器限制從腳本發起的跨來源 HTTP 請求。例如，fetch() 和 XMLHttpRequest 遵循同源政策。這意味著，使用這些 API 的 Web 應用程序只能請求與加載該應用程序的相同來源的資源，除非來自其他來源的回應包含正確的 CORS 標頭。

這裡所說的 **包含正確的 CORS 標頭** ，以我們的範例來說指的就是用 **跨域請求** 的方式取得 Amazon S3 上的圖片時，Response headers 需要回傳`Access-Control-Allow-Origin: *`

所以整個觸發 CORS 錯誤的過程是這樣的：

1. 一開始進入畫面時，瀏覽器就下載並快取了 **非跨域請求** 方式的圖片

2. 後續下載圖片使用 `fetch` 請求，由於我們網站自身的網址與 Amazon S3 所在圖片伺服器的網址[不同源](https://developer.mozilla.org/zh-TW/docs/Web/Security/Same-origin_policy)，代表 `fetch` 需要以 **跨域請求** 的方式獲取圖片

3. 此時因為已經有步驟 1. 中以 **非跨域請求** 方式快取的圖片了，瀏覽器直接以快取回應，但之前快取的 **非跨域請求** 並沒有 **包含正確的 CORS 標頭** 也就是 `Access-Control-Allow-Origin: *`

4. 結果導致 CORS 錯誤

此時在 Chrome DevTools 中可以看到 `fetch` 發出的 request 顯示 [Provisional headers are shown...](https://developer.chrome.com/docs/devtools/network/reference?utm_source=devtools&hl=zh-cn#provisional-headers) 代表 `fetch` 圖片的請求並沒有真正的傳遞到伺服器，而是使用到了之前 **非跨域請求** 方式取得的圖片快取

<div style="display: flex; justify-content: center;">
  <img src="./provisional-header.png" />
</div>

## 解決方式 - 如何讓圖片下載成功？

既然知道了原因是 `fetch` 下載圖片時用到 **非跨域請求** 方式的快取圖片，可以想到有以下幾種解決方式：

#### 1. fetch 時指定 cache 不使用快取圖片

在使用 `fetch` 時可以設置參數 [cache](https://developer.mozilla.org/zh-CN/docs/Web/API/Request/cache)，指定請求資源時是否覆用快取，以我們的例子來說，目的是在 `fetch` 的時候不要用到原本的快取，所以適合設定的值是 `no-store` 或 `reload`

```js
const response = await fetch(image.url, { cache: 'no-store' }); // or 'reload'
```

#### 2. 設定 Vary: Origin

Response headers 中的 [Vary](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Vary) 欄位，作用是即使同一個 url 但只要 Request headers 的某個欄位與 `Vary` 中設定的字串中有不一致的地方，那麼這個 Request 就不會使用到快取而是會真正的向伺服器發出請求，有點難用文字說明，以下用例子來解釋比較清楚：

1. 一進到頁面時 `<img src="..." />` 的元素讓瀏覽器前往伺服器下載圖片，此時的 Request headers 由於是 **非跨域請求** 所以並沒有攜帶 `Origin: localhost:8081` 這樣的欄位，接著瀏覽器在下載完圖片後以 **非跨域請求** 方式將圖片快取起來

2. 接著點擊下載按鈕呼叫 `fetch` 請求圖片時，由於網站本身的網址與圖片所在的網址是非同源的，因此下載圖片的行為是 **跨域請求**，會在 Request headers 加上 `Origin: localhost:8081`

3. 第 1 項快取的圖片在 Request headers 中 **沒有** `Origin: localhost:8081`，第 2 項快取的圖片在 Request headers 中 **有** `Origin: localhost:8081`

4. 當執行 `fetch` 時，由於原先快取的 Request headers 跟目前請求的 Request headers 其 `Origin` 欄位的值不一樣，而 `Vary: Origin` 的設定在此時會判斷 `Origin` 的值 **改變了**，就不會覆用原先的快取，而是前往伺服器 **跨域請求** 圖片

- 第 1 項的 `<img src="..." />` 是沒有 `Origin` 欄位的 **非跨域請求**，但後端伺服器設定 Response headers 帶有 `Vary: Origin`
<div style="display: flex; justify-content: center; margin-top: -1.2em">
  <img src="./vary-origin-first.png" />
</div>

- 第 2 項 `fetch` 是帶有 `Origin` 欄位的 **跨域請求**，判斷 `Origin` 欄位的值改變了，所以不會覆用快取而是對伺服器發出真正的請求，順利取得 `Access-Control-Allow-Origin: *`
<div style="display: flex; justify-content: center; margin-top: -1.2em">
  <img src="./vary-origin-second.png" />
</div>

順帶一提在 [fetch 規範中](https://fetch.spec.whatwg.org/#cors-protocol-and-http-caches) 剛好有一段就完美的解釋我們所遇到的問題，以及如何使用 `Vary: Origin` 解決

> In particular, consider what happens if `Vary` is not used and a server is configured to send `Access-Control-Allow-Origin` for a certain resource only in response to a CORS request. When a user agent receives a response to a non-CORS request for that resource (for example, as the result of a navigation request), the response will lack `Access-Control-Allow-Origin` and the user agent will cache that response. Then, if the user agent subsequently encounters a CORS request for the resource, it will use that cached response from the previous non-CORS request, without `Access-Control-Allow-Origin`.

#### 3. crossOrigin='anonymous'

前面兩種方式在第一次進入頁面時就向伺服器拿到了 **非跨域請求** 的圖片，而之後 `fetch` 時為了避免覆用快取導致 CORS error 又向伺服器 **跨域請求** 了一次圖片，這等於我們花時間向伺服器拿了兩次一模一樣的圖片，完全沒有使用到快取的好處

換個方向想，如果 `fetch` 時需要 **跨域請求** 圖片的話，那為何不一開始進入頁面的時候就直接 **跨域請求** 呢？這就是 `crossOrigin='anonymous'` 的作用

將 [crossOrigin](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Reference/Attributes/crossorigin) 設置成 `crossOrigin='anonymous'` 或是 `crossOrigin=''` 是一樣的，代表需要以 **跨域請求** 的方式獲取圖片

```html
<img
  src="https://rjzdhlvuuafqvgoyticm.supabase.co/functions/v1/getCORSImage?image=building.jpg"
  crossorigin="anonymous"
  alt="building"
/>
```

## 結論

在這篇文章的範例中，`fetch` 下載圖片時需要送出的是 **跨域請求**，但卻覆用到一開始 **非跨域請求** 的圖片快取，所以才引起 CORS 錯誤。接著提出了三個想法解決，最終採用在 `img` 元素設置 `crossOrigin='anonymous'` 屬性的方式讓一進入頁面時就用 **跨域請求** 的方式快取圖片，這樣的方式可以覆用每次的圖片快取又可以解決 CORS 錯誤的問題

## 範例

[範例程式碼 repo](https://github.com/bcjohnblue/react-image-cors)
<a href="https://bcjohnblue.github.io/react-image-cors/" target="_blank">線上 Demo</a>

## 參考資料

1. [原來 CORS 沒有我想像中的簡單](https://blog.huli.tw/2018/08/18/cors-is-hard/)

huli 大大寫的文章，跟這篇文章遇到的問題非常類似，都是讀取到原先瀏覽器快取住的檔案導致 CORS error
