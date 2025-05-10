---
title: 使用原生的 queueMicrotask 處理微任務
date: 2024-02-04 11:52:47
categories: Javascript
tags:
---

此為 **event loop** 系列文章 - 第 3 篇：

1. <a href="/2024/01/20/javascript-中的-event-loop-及瀏覽器渲染機制" target="_blank" rel="noreferrer noopenner">Javascript 中的 event loop 及瀏覽器渲染機制</a> 
2. <a href="/2024/01/27/從程式碼角度來看-event-loop" target="_blank" rel="noreferrer noopenner">從程式碼角度來看 event loop</a> 
3. <a href="/2024/02/04/使用原生的-queuemicrotask-處理微任務/" target="_blank" rel="noreferrer noopenner">使用原生的 queueMicrotask 處理微任務</a>
3. <a href="/2024/02/05/vue-nexttick-中的-event-loop/" target="_blank" rel="noreferrer noopenner">Vue.nextTick() 中的 event loop</a>

## 前言
在研究 **event loop** 的過程中，赫然發現原來瀏覽器已經有了原生的 [queueMicrotask](https://developer.mozilla.org/en-US/docs/Web/API/queueMicrotask) 讓開發者可以自行管理微任務的執行，下面我們來看看什麼情況下會需要用到這個功能吧

## 基本用法
基本上 `queueMicrotask` 就如同使用 `new Promise()` 一樣，會將 callback 加入到 **微任務佇列 (microtask queue)**
```js
let callback = () => console.log("Regular timeout callback has run");

let urgentCallback = () =>  console.log("*** Oh noes! An urgent callback has run!");

console.log("Main program started");
setTimeout(callback, 0);
queueMicrotask(urgentCallback);
console.log("Main program exiting");
```

<!--more-->

**結果：**
```js
'Main program started'
'Main program exiting'
'*** Oh noes! An urgent callback has run!'
'Regular timeout callback has run'
```

## 何時會使用到 queueMicrotask
基本上大部分的狀況下，開發者們都不太需要使用到 `queueMicrotask`，但 MDN 上提到一個有趣的範例，為了保證程式的執行順序，在某些狀況下適合使用到 `queueMicrotask` 將某些任務的優先程度提高，提前執行

假設有一個 DOM 元素添加了自定義的 `load` 事件，當執行這個元素的 `getData()` 方法後會利用 [dispatchEvent](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/dispatchEvent) 送出一個事件，通知資料已經載完了(第 1 行)
```js
element.addEventListener("load", () => console.log("Loaded data"));
console.log("Fetching data…");
element.getData();
console.log("Data fetched");
```

```js
customElement.prototype.getData = (url) => {
  if (this.cache[url]) {
    this.data = this.cache[url];
    this.dispatchEvent(new Event("load"));
  } else {
    fetch(url)
      .then((result) => result.arrayBuffer())
      .then((data) => {
        this.cache[url] = data;
        this.data = data;
        this.dispatchEvent(new Event("load"));
      });
  }
};
```
由於 [dispatchEvent 發出的事件是同步執行的](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/dispatchEvent#:~:text=Unlike%20%22native%22%20events%2C%20which%20are%20fired%20by%20the%20browser%20and%20invoke%20event%20handlers%20asynchronously%20via%20the%20event%20loop%2C%20dispatchEvent()%20invokes%20event%20handlers%20synchronously.%20All%20applicable%20event%20handlers%20are%20called%20and%20return%20before%20dispatchEvent()%20returns.)，並不像一般 WebAPI 原生事件(click, mouseenter 等) 是異步執行的 **宏任務 (macrotask)** ，所以以上程式碼的執行順序如下：
1. 首先執行第 2 行，印出 <span class="log">Fetching data…</span>
2. 執行第 3 行的 `getData()` 函式，第一次執行 `getData()` 時由於沒有 cache 所以會執行第 6 行的 `fetch` 等待資料下載完成
3. 等待 `fetch` 異步執行獲取資料的過程中，第 4 行會先執行印出 <span class="log">Data fetched</span>
4. 最後資料載入完成後執行 `this.dispatchEvent(new Event("load"));`，回到第 1 行的 callback 印出 <span class="log">Loaded data</span>

<!-- 函式裡最終會執行 `this.dispatchEvent(new Event("load"));`，告知其他訂閱對象資料已經下載完成了 -->
<!-- 3. 由於 `dispatchEvent` 是同步執行的，第 1 行中的 callback 會立即執行印出 <span class="log">Loaded data</span> -->

**結果**
```js
'Fetching data…'
'Data fetched'
'Loaded data'
```

但如果之後執行 `getData()` 資料已經有 cache 的狀況下，執行過程如下：
1. 首先執行第 2 行，印出 <span class="log">Fetching data…</span>
2. 執行第 3 行的 `getData()` 函式，此時執行 `getData()` 時由於已經有 cache 所以會直接執行到第 4 行的 `this.dispatchEvent(new Event("load"));` 發出 load event
3. 由於 `dispatchEvent` 是同步執行的，第 1 行中的 callback 會立即執行印出 <span class="log">Loaded data</span>
4. `getData()` 函式結束，接著執行第 4 行印出 <span class="log">Data fetched</span>

**結果**
```js
'Fetching data…'
'Loaded data'
'Data fetched'
```

哇！我們可以看到有無 cache 時，會導致程式碼的執行順序不一，這一定不是我們期望看到的，此時就可以考慮使用 `queueMicrotask`(第 3 行)，使原本的同步事件改為異步事件執行，這樣不論有無 cache 的狀況下都可以保證一樣的執行順序

```js
customElement.prototype.getData = (url) => {
  if (this.cache[url]) {
    queueMicrotask(() => {
      this.data = this.cache[url];
      this.dispatchEvent(new Event("load"));
    });
  } else {
    fetch(url)
      .then((result) => result.arrayBuffer())
      .then((data) => {
        this.cache[url] = data;
        this.data = data;
        this.dispatchEvent(new Event("load"));
      });
  }
};
```

**結果**
這樣不論有無 cache 都會是
```js
'Fetching data…'
'Data fetched'
'Loaded data'
```

#### 小結
一開始覺得 MDN 提出的這個範例蠻有趣的，在這種場合下用 `queueMicrotask` 就可以保證不論是否有 cache 的狀況下都有一致的執行順序，但我後來想想這個範例寫得似乎有點奇怪，仔細想想通常在使用 `fetch` 呼叫 api 時都會添加 `await` 等待資料回傳回來再執行後面的程式，如此一來應該就不必特別用到 `queueMicrotask` 了吧？

```js
element.addEventListener("load", () => console.log("Loaded data"));
console.log("Fetching data…");
await element.getData();
console.log("Data fetched");
```

```js
customElement.prototype.getData = (url) => {
  return new Promise((resolve) => {
    if (this.cache[url]) {
      this.data = this.cache[url];
      this.dispatchEvent(new Event("load"));
      resolve();
    } else {
      fetch(url)
        .then((result) => result.arrayBuffer())
        .then((data) => {
          this.cache[url] = data;
          this.data = data;
          this.dispatchEvent(new Event("load"));
          resolve();
        });
    }
  })
};
```

**結果**
```js
'Fetching data…'
'Loaded data'
'Data fetched'
```
不論有無 cache 應該都會是這個順序，但或許 MDN 上的範例想要的執行順序是 `'Data fetched'` 在 `'Loaded data'` 前執行，如果是這樣的話就的確需要用到 `queueMicrotask` 了


## 參考資料
- [Using microtasks in JavaScript with queueMicrotask()](https://developer.mozilla.org/en-US/docs/Web/API/HTML_DOM_API/Microtask_guide)

{% raw %}
<style>
.log {
	color: #9c9;
  font-weight: bold;
}
</style>
{% endraw %}