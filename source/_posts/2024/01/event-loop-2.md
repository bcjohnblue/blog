---
title: 從程式碼角度來看 event loop
date: 2024-01-27 12:08:56
categories: Javascript
tags: 面試題
---

此為 **event loop** 系列文章 - 第 2 篇：

1. <a href="/blog/2024/01/20/javascript-中的-event-loop-及瀏覽器渲染機制" target="_blank" rel="noreferrer noopenner">Javascript 中的 event loop 及瀏覽器渲染機制</a> 
2. <a href="/blog/2024/01/27/從程式碼角度來看-event-loop" target="_blank" rel="noreferrer noopenner">從程式碼角度來看 event loop</a> 
3. <a href="/blog/2024/02/04/使用原生的-queuemicrotask-處理微任務/" target="_blank" rel="noreferrer noopenner">使用原生的 queueMicrotask 處理微任務</a>
3. <a href="/blog/2024/02/05/vue-nexttick-中的-event-loop/" target="_blank" rel="noreferrer noopenner">Vue.nextTick() 中的 event loop</a>

## 前言
上一篇介紹了 **event loop** 的運行原理，這篇文章希望藉由各種範例進一步體會 **event loop** 的執行順序

## 各種不同的 event loop 範例

#### 1. 基本的 event loop 執行順序
```js
setTimeout(() => {
  console.log('timeout')
}, 0)

Promise.resolve().then(() => {
  console.log('promise');
});

console.log('main script');
```

<!--more-->

**步驟：**
1. 第 1 行的 `setTimeout` `callback` 會放入 **宏任務佇列 (macrotask queue)** 裡，等待之後執行
2. 第 5 行的 `promise` `callback` 會放入 **微任務佇列 (microtask queue)** 裡，等待之後執行
3. 第 9 行的 `main script` 同步執行，印出 <span class="log">main script</span>
4. **event loop** 挑出當輪 **微任務佇列 (microtask queue)** 的所有 **微任務 (microtask)** ，執行步驟 2. 的 `callback`，印出 <span class="log">promise</span>
5. **微任務佇列 (microtask queue)** 為空，進入下一輪的 **event loop** 循環
6. 此輪從 **宏任務佇列 (macrotask queue)** 挑出步驟 2. 的 **宏任務 (macrotask)** 執行，印出 <span class="log">timeout</span>

**結果：**
```js
'main script'
'promise'
'timeout'
```

#### 2. 基本的 promise 執行順序
[來源](https://github.com/LinDaiDai/niubility-coding-js/blob/master/JavaScript/%E5%BC%82%E6%AD%A5/%E8%A6%81%E5%B0%B1%E6%9D%A545%E9%81%93Promise%E9%9D%A2%E8%AF%95%E9%A2%98%E4%B8%80%E6%AC%A1%E7%88%BD%E5%88%B0%E5%BA%95.md#12-%E9%A2%98%E7%9B%AE%E4%BA%8C)
```js
const promise = new Promise((resolve, reject) => {
  console.log(1);
  resolve('success')
  console.log(2);
});
promise.then(() => {
  console.log(3);
});
console.log(4);
```
**步驟：**
1. 首先執行第 1 行的 `new Promise` 
2. 第 2 行印出 <span class="log">1</span>
3. 第 3 行將 `promise` 的狀態設為 `resolved`，值為 `success`
4. 繼續往下執行 `promise` 裡的程式，遇到第 4 行印出 <span class="log">2</span>
5. 第 6 行 `promise` 的狀態已經為 `resolved`，將 `then` 後面的 `callback` 放入 **微任務佇列 (microtask queue)** 中
6. 執行第 9 行，印出 <span class="log">4</span>
7. **event loop** 挑出當輪 **微任務佇列 (microtask queue)** 的所有 **微任務 (microtask)** => 執行步驟 5. 的 `callback`
8. 執行第 7 行，印出 <span class="log">3</span>

**結果：**
```js
1
2
4
3
```

#### 3. async await 寫法的 promise
[來源](https://github.com/LinDaiDai/niubility-coding-js/blob/master/JavaScript/%E5%BC%82%E6%AD%A5/%E8%A6%81%E5%B0%B1%E6%9D%A545%E9%81%93Promise%E9%9D%A2%E8%AF%95%E9%A2%98%E4%B8%80%E6%AC%A1%E7%88%BD%E5%88%B0%E5%BA%95.md#51-%E9%A2%98%E7%9B%AE%E4%B8%80)
```js
async function async1() {
  console.log("async1 start");
  await async2();
  console.log("async1 end");
}
async function async2() {
  console.log("async2");
}
async1();
console.log('start')
```
**分析**
由於 `async await` 是 `promise` 的語法糖，所以以下兩種寫法是等價的：
```js
async function async2() {
  console.log("async2");
}

await async2();
console.log("async1 end");
```
```js
const async2 = new Promise((resolve) => {
  console.log("async2");
}).then(() => {
  console.log("async1 end");
});
```

**步驟：**
1. 執行第 9 行 `async1` 函式
2. 進入 `async1` 函式，執行第 2 行，印出 <span class="log">async1 start</span>
3. 執行第 3 行 `async2` 函式
4. 進入 `async2` 函式，執行第 7 行，印出 <span class="log">async2</span>
5. `async2` 函式結束回傳 `Promise {<fulfilled>: undefined}`
6. 回到第 3 行 `async2` 函式，將第 3 行後的程式放入 **微任務佇列 (microtask queue)** 中
7. `async1` 函式結束，回到第 9 行
8. 執行第 10 行，印出 <span class="log">start</span>
9. **event loop** 挑出當輪 **微任務佇列 (microtask queue)** 的所有 **微任務 (microtask)** => 執行步驟 6. 的程式
10. 執行第 4 行，印出 <span class="log">async1 end</span>

**結果：**
```js
'async1 start'
'async2'
'start'
'async1 end'
```

#### 4. 在 promise 中添加長時間的任務執行
[來源](https://www.zhihu.com/question/36972010)
```js
setTimeout(function () {
  console.log(4);
}, 0);

new Promise(function (resolve) {
  console.log(1);
  for (var i = 0; i < 10000; i++) {
    i == 9999 && resolve();
  }
  console.log(2);
}).then(function () {
  console.log(5);
});

console.log(3);
```
**步驟：**
1. 執行第 1 行將 `setTimeout` 的 `callback` 放入 **宏任務佇列 (macrotask queue)** 中
2. 執行第 5 行的 `promise`
3. 執行第 6 行，印出 <span class="log">1</span>
4. 第 7-9 行，執行一段長時間的程式後第 8 行將 `promise` `resolve`
5. 執行第 10 行，印出 <span class="log">2</span>
6. 將第 11 行 `then` 後的 `callback` 放入 **微任務佇列 (microtask queue)** 中
7. 執行第 15 行，印出 <span class="log">3</span>
8. **event loop** 挑出當輪 **微任務佇列 (microtask queue)** 的所有 **微任務 (microtask)** => 執行步驟 6. 的 `callback`
9. 執行第 12 行，印出 <span class="log">5</span>
10. 當輪 **event loop** 結束
&nbsp;
11. **event loop** 挑出下一輪 **宏任務佇列 (macrotask queue)** 中的第一個 **宏任務 (macrotask)** => 執行步驟 1. 的 `callback`
12. 執行第 2 行，印出 <span class="log">4</span>

**結果：**
```js
1
2
3
5
4
```

#### 5. promise 與 setTimeout 的互相執行
[來源](https://github.com/LinDaiDai/niubility-coding-js/blob/master/JavaScript/%E5%BC%82%E6%AD%A5/%E8%A6%81%E5%B0%B1%E6%9D%A545%E9%81%93Promise%E9%9D%A2%E8%AF%95%E9%A2%98%E4%B8%80%E6%AC%A1%E7%88%BD%E5%88%B0%E5%BA%95.md#23-%E9%A2%98%E7%9B%AE%E4%B8%89-1)
```js
Promise.resolve().then(() => {
  console.log('promise1');
  const timer2 = setTimeout(() => {
    console.log('timer2')
  }, 0)
});
const timer1 = setTimeout(() => {
  console.log('timer1')
  Promise.resolve().then(() => {
    console.log('promise2')
  })
}, 0)
console.log('start');
```
**步驟：**
1. 首先第 1 行將 `promise.resolve` 後的 `callback` 放入 **微任務佇列 (microtask queue)** 中
2. 執行第 7 行將 `timer1` 的 `callback` 放入 **宏任務佇列 (macrotask queue)** 中
3. 執行第 13 行，印出 <span class="log">start</span>
4. **event loop** 挑出當輪 **微任務佇列 (microtask queue)** 的所有 **微任務 (microtask)** => 執行步驟 1. 的 `callback`
5. 執行第 2 行，印出 <span class="log">promise1</span>
6. 執行第 3 行將 `timer2` 的 `callback` 放入 **宏任務佇列 (macrotask queue)** 中
7. 當輪 **event loop** 結束
&nbsp;
8. **event loop** 挑出下一輪 **宏任務佇列 (macrotask queue)** 中的第一個 **宏任務 (macrotask)** => 執行步驟 2. 的 `callback`
9. 執行第 8 行，印出 <span class="log">timer1</span>
10. 執行第 9 行將 `promise.resolve` 後的 `callback` 放入 **微任務佇列 (microtask queue)** 中
11. **event loop** 挑出當輪 **微任務佇列 (microtask queue)** 的所有 **微任務 (microtask)** => 執行步驟 10. 的 `callback`
12. 執行第 10 行，印出 <span class="log">promise2</span>
13. 當輪 **event loop** 結束
&nbsp;
14. **event loop** 挑出下一輪 **宏任務佇列 (macrotask queue)** 的所有 **宏任務 (macrotask)** => 執行步驟 6. 的 `callback`
15. 執行第 4 行，印出 <span class="log">timer2</span>

**結果：**
```js
'start'
'promise1'
'timer1'
'promise2'
'timer2'
```

## 不同狀況下的 event loop 範例

以上範例單純只牽涉到 **宏任務 (macrotask)** 與 **微任務 (microtask)** 時，根據 **event loop** 的規則，執行的順序會是固定的，但如果牽涉到頁面的渲染，使用到 `requestAnimationFrame` 時，執行的順序就不是那麼絕對的了，以下我們來看看這些狀況：

#### 1. setTimeout 與 requestAnimationFrame
[來源](https://intspirit.medium.com/understanding-the-browsers-event-loop-for-building-high-performance-web-applications-part-1-fe4b573a1520#:~:text=the%20rendering%20step.-,Quiz%201.,-Try%20to%20guess)
```js
setTimeout(() => {
  console.log('timeout 1');
  document.body.innerHTML = 'A';
});

requestAnimationFrame(() => {
  console.log('requestAnimationFrame');
  document.body.innerHTML = 'B';
});

setTimeout(() => {
  console.log('timeout 2');
  document.body.innerHTML = 'C';
});

console.log('script');
document.body.innerHTML = 'D';
```

首先這一段程式執行後，畫面最後印出來的會是 A, B, C 或是 D 呢？

<img src="/blog/2024/01/20/javascript-中的-event-loop-及瀏覽器渲染機制/event-loop-60fps.png" width="50%">

先貼一張 <a href="/blog/2024/01/20/javascript-中的-event-loop-及瀏覽器渲染機制#event-loop-的全貌" target="_blank" rel="noreferrer noopenner">Javascript 中的 event loop 及瀏覽器渲染機制</a> 來複習 **event loop** 循環的全貌，以上程式使用到的 `requestAnimationFrame` 會在每一幀渲染畫面 **之前** 執行，所以我們會認為它執行的順序是：

**步驟：**
1. 第 1 行的 `setTimeout` `callback` 會放入 **宏任務佇列 (macrotask queue)** 裡，等待之後執行
2. 第 6 行的 `requestAnimationFrame` `callback` 會等待下一次畫面渲染前執行
3. 第 11 行的 `setTimeout` `callback` 會放入 **宏任務佇列 (macrotask queue)** 裡，等待之後執行
4. 執行第 16, 17 行
5. 接著當輪的 **宏任務佇列 (macrotask queue)** 都執行完了，所以會看 **微任務佇列 (microtask queue)** 中有沒有 **微任務 (microtask)** 要執行
6. 因爲程式碼中沒有 **微任務 (microtask)** => **微任務佇列 (microtask queue)** 為空 => 沒有 **微任務 (microtask)** 需要處理
7. 預備進行畫面渲染，執行第 6 行的 `requestAnimationFrame`
8. 進入 **Style** => **Layout** => **Paint** => **Composite** 這些階段的頁面渲染
9. 當輪 **event loop** 結束
&nbsp;
9. 下一輪的 **event loop** 從 **宏任務佇列 (macrotask queue)** 挑出 **宏任務 (macrotask)** 執行 => 執行步驟 1. 的 `setTimeout` `callback`
10. 此輪的 **event loop** 已經沒有後續任務了，當輪 **event loop** 結束
&nbsp;
11. 再下一輪的 **event loop** 從 **宏任務佇列 (macrotask queue)** 挑出 **宏任務 (macrotask)** 執行 => 執行步驟 3. 的 `setTimeout` `callback`

**結果：**
由以上步驟來看最後執行的應該是第 13 行，所以最後畫面會顯示的是 C，但當你把這段程式碼多次執行時，會發現最後顯示的不一定是 C，有時候畫面上顯示的會是 B，這是為什麼呢？

關鍵就在於瀏覽器什麼時候渲染畫面，上一篇文章 <a href="/blog/2024/01/20/javascript-中的-event-loop-及瀏覽器渲染機制/#event-loop-如何安排任務的執行順序" target="_blank" rel="noreferrer noopenner">event loop 如何安排任務的執行順序</a> 中提到在 **微任務 (microtask)** 都執行完後，下一步會判斷**是否需要渲染 UI 畫面**，而這就是所謂的關鍵點了
> [瀏覽器可能出於各種原因，盡量高效的渲染畫面，只在必要的時候才進行渲染][1]

因此當瀏覽器判斷第一輪執行 **event loop** 後，如果還不需要渲染畫面，那麼也就不會執行步驟 2. `requestAnimationFrame` 裡的 `callback`，這種狀況下，步驟 9. 及步驟 11. 就會先被執行，最後瀏覽器判斷需要渲染畫面時才會執行步驟 2.，因此就會看到畫面上最後顯示的是 B

#### 2. 被延遲執行的 requestAnimationFrame
上一個範例我們看到瀏覽器可能會自行判斷在當下的 **event loop** 結束後，是否需要進行渲染畫面，因此也導致 `requestAnimationFrame` 的執行時機不確定，第 2 個範例我們來看看如果在 `requestAnimationFrame` 執行之前有耗時較長的任務需要處理，會有什麼樣的結果
```js
const longTask = (ms = 500) => {
  const start = performance.now();
  while (true) {
    if (performance.now() - start > ms) {
      break;
    }
  }
};

const start = performance.now();

setTimeout(() => {
  console.log('timeout 1');
  document.body.innerHTML = 'A';
  longTask();
});

requestAnimationFrame(() => {
  console.log('requestAnimationFrame', performance.now() - start);
  document.body.innerHTML = 'B';
});

setTimeout(() => {
  console.log('timeout 2');
  document.body.innerHTML = 'C';
  longTask();
});

console.log('script');
document.body.innerHTML = 'D';
longTask();
```

首先這裡我們寫了一個 `longTask` 的函式，模擬長時間的運算(500ms)，這裡我們打算在每個 **宏任務 (macrotask)** 執行後，再執行一次 `longTask`，這樣會導致每輪的 **event loop** 結束後都經過了 500ms，而這時間遠遠超過一幀 (17ms) 瀏覽器判斷應該進行畫面渲染的時機，所以每輪 **宏任務 (macrotask)** 執行後瀏覽器都會渲染畫面

**順序：**
1. 首先執行第 29-31 行，因為 31 行添加了一個長時間的任務，可以保證瀏覽器在執行 31 行後會重新渲染畫面
2. 在渲染畫面前會執行第 18-21 行的 `requestAnimationFrame` `callback`，第 20 行會將接下來的畫面印出 B
3. 執行第 12-16 行，因為 15 行添加了一個長時間的任務，所以瀏覽器會執行渲染，這一輪的畫面將會印出 A
4. 執行第 23-27 行，因為 26 行添加了一個長時間的任務，所以瀏覽器會執行渲染，這一輪的畫面將會印出 C

**結果：**
1. 因為每輪 **event loop** 都添加了 500ms 的長時間任務，所以每輪 **event loop** 執行後瀏覽器會判斷需要渲染畫面，可以明顯地看到 B => A => C 依序印出
2. 第 30 行其實沒有任何作用，因為在頁面渲染前會執行到第 20 行，將原本第 30 行覆蓋過去
3. 第 19 行可以看到 `requestAnimationFrame` 的執行時機在長時間任務執行下，有可能會被延遲到 500ms 後，不會再是每 17ms 都執行一次

## 參考資料
- [要就来45道Promise面试题一次爽到底](https://github.com/LinDaiDai/niubility-coding-js/blob/master/JavaScript/%E5%BC%82%E6%AD%A5/%E8%A6%81%E5%B0%B1%E6%9D%A545%E9%81%93Promise%E9%9D%A2%E8%AF%95%E9%A2%98%E4%B8%80%E6%AC%A1%E7%88%BD%E5%88%B0%E5%BA%95.md#51-%E9%A2%98%E7%9B%AE%E4%B8%80)
這篇文章的大部分範例都引用這裡，看完這裡面的 45 道題後，一定對於整個 event loop 的執行順序更為熟悉

- [requestAnimationFrame 执行的困惑](https://hentaicracker.github.io/2020/rAF.html#_2-requestanimationframe-%E6%89%A7%E8%A1%8C%E7%9A%84%E5%9B%B0%E6%83%91)

- [Understanding the browser's Event Loop for building high-performance web applications. Part 1.](https://intspirit.medium.com/understanding-the-browsers-event-loop-for-building-high-performance-web-applications-part-1-fe4b573a1520)

- [Which queue is associated with requestAnimationFrame?](https://stackoverflow.com/questions/77008112/which-queue-is-associated-with-requestanimationframe)
宏任務(microtask) 有 macrotask queue，微任務(microtask) 有 macrotask queue，但 requestAnimationFrame 的 callback 會被放在哪裡等待後續執行呢？

[1]: https://hentaicracker.github.io/2020/rAF.html#_3-5-%E6%9B%B4%E6%96%B0%E6%B8%B2%E6%9F%93

{% raw %}
<style>
.log {
	color: #9c9;
  font-weight: bold;
}
</style>
{% endraw %}