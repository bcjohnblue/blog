---
title: Vue.nextTick() 中的 event loop
date: 2024-02-05 09:47:51
categories: Javascript
tags:
---

此為 **event loop** 系列文章 - 第 4 篇：

1. <a href="/blog/2024/01/20/javascript-中的-event-loop-及瀏覽器渲染機制" target="_blank" rel="noreferrer noopenner">Javascript 中的 event loop 及瀏覽器渲染機制</a> 
2. <a href="/blog/2024/01/27/從程式碼角度來看-event-loop" target="_blank" rel="noreferrer noopenner">從程式碼角度來看 event loop</a> 
3. <a href="/blog/2024/02/04/使用原生的-queuemicrotask-處理微任務/" target="_blank" rel="noreferrer noopenner">使用原生的 queueMicrotask 處理微任務</a>
3. <a href="/blog/2024/02/05/vue-nexttick-中的-event-loop/" target="_blank" rel="noreferrer noopenner">Vue.nextTick() 中的 event loop</a>

## 前言
這篇文章想藉由閱讀 [Vue.nextTick()](https://vuejs.org/api/general.html#nexttick) 的源碼來看 **event loop** 的使用

## Vue.nextTick 的使用方式
官方文件寫明 `Vue.nextTick()` 是拿來等待下一次 DOM 更新的方法，因為 Vue 在每次響應式數據改變後是異步去更新 DOM，所以如果在數據改變後，馬上獲取 DOM 的資料會是舊的，這時就需要用到 `Vue.nextTick()` 獲取更新後的 DOM

```js
<script setup>
import { ref, nextTick } from 'vue'

const count = ref(0)

async function increment() {
  count.value++

  // DOM 还未更新
  console.log(document.getElementById('counter').textContent) // 0

  await nextTick()
  
  // DOM 此时已经更新
  console.log(document.getElementById('counter').textContent) // 1
}
</script>

<template>
  <button id="counter" @click="increment">{{ count }}</button>
</template>
```

<!--more-->

## Vue.nextTick 的源碼分析
目前最新版(2024/02) 的 [Vue.nextTick 源碼](https://github.com/vuejs/vue/blob/dev/src/core/util/next-tick.js) 如下(移除掉一些註解方便整體閱讀)：
```js
import { noop } from 'shared/util'
import { handleError } from './error'
import { isIE, isIOS, isNative } from './env'

export let isUsingMicroTask = false

const callbacks = []
let pending = false

function flushCallbacks () {
  pending = false
  const copies = callbacks.slice(0)
  callbacks.length = 0
  for (let i = 0; i < copies.length; i++) {
    copies[i]()
  }
}

let timerFunc

if (typeof Promise !== 'undefined' && isNative(Promise)) {
  const p = Promise.resolve()
  timerFunc = () => {
    p.then(flushCallbacks)
    if (isIOS) setTimeout(noop)
  }
  isUsingMicroTask = true
} else if (!isIE && typeof MutationObserver !== 'undefined' && (
  isNative(MutationObserver) ||
  MutationObserver.toString() === '[object MutationObserverConstructor]'
)) {
  let counter = 1
  const observer = new MutationObserver(flushCallbacks)
  const textNode = document.createTextNode(String(counter))
  observer.observe(textNode, {
    characterData: true
  })
  timerFunc = () => {
    counter = (counter + 1) % 2
    textNode.data = String(counter)
  }
  isUsingMicroTask = true
} else if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
  // Fallback to setImmediate.
  // Technically it leverages the (macro) task queue,
  // but it is still a better choice than setTimeout.
  timerFunc = () => {
    setImmediate(flushCallbacks)
  }
} else {
  // Fallback to setTimeout.
  timerFunc = () => {
    setTimeout(flushCallbacks, 0)
  }
}

export function nextTick (cb?: Function, ctx?: Object) {
  let _resolve
  callbacks.push(() => {
    if (cb) {
      try {
        cb.call(ctx)
      } catch (e) {
        handleError(e, ctx, 'nextTick')
      }
    } else if (_resolve) {
      _resolve(ctx)
    }
  })
  if (!pending) {
    pending = true
    timerFunc()
  }
  if (!cb && typeof Promise !== 'undefined') {
    return new Promise(resolve => {
      _resolve = resolve
    })
  }
}
```

### nextTick 函式
第 57-79 行就是我們實際在用的 `nextTick` 函式，傳入的參數有兩個，`cb` 是 DOM 更新後才執行的 callback，`ctx` 為了傳遞 `this` 的指向
第 59-69 行將傳入的 `cb` 放入 `callbacks` 的佇列裡，等待後續執行
第 70-73 行用一個 `pending` 的變數控制，讓多次呼叫 `nextTick` 函式時，`timerFunc` 可以在同一次的 **更新時機(tick)** 中執行所有的 `callbacks`

**什麼是更新時機(tick)?**
在 `Vue` 中定義了一個叫做 **tick** 的專有名詞，指的是某一個特定的時間下 `Vue` 用來執行響應式資料改變、DOM 更新等邏輯，**tick** 執行的時機會根據之後將提到的 `timerFunc` 函式判斷是要用 **event loop** 中的 **宏任務 (macrotask)** 或是 **微任務 (microtask)** 方式執行。

第 74-78 行讓 `nextTick` 函式可以單純回傳 `Promise`，如此一來不用傳 `cb` 也可以使用

- 使用 callback 方式
```js
console.log(document.getElementById('counter').textContent) // 更新 DOM 前
await nextTick(() => {
  console.log(document.getElementById('counter').textContent) // 更新 DOM 後
})
```

- 不使用 callback 方式
```js
console.log(document.getElementById('counter').textContent) // 更新 DOM 前
await nextTick()
console.log(document.getElementById('counter').textContent) // 更新 DOM 後
```

### callbacks & flushCallbacks - 負責執行 callback
在 `nextTick` 中丟入的 `cb` 參數，會放入 `callbacks` 佇列裡，等待下一次適當的 **更新時機(nextTick)** 後，才真正執行 `cb` 函式。而真正執行 `cb` 的地方就是 `flushCallbacks` 函式
```js
const callbacks = []
function flushCallbacks () {
  pending = false
  // 問題 2. 為什麼需要複製 callbacks 陣列？
  const copies = callbacks.slice(0)
  callbacks.length = 0
  for (let i = 0; i < copies.length; i++) {
    copies[i]()
  }
}
```

### timerFunc - 決定用哪種 event loop 方式決定更新時機(nextTick)
什麼是下一次適當的 **更新時機(nextTick)** 呢？在 `Vue` 中使用了 `timerFunc` 這個變數去做判斷，以下這段程式碼會根據不同的瀏覽器去做兼容控制，我們可以看到 `timerFunc` 的優先順序為： `Promise` => `MutationObserver` => `setImmediate` => `setTimeout`，也就是說 `nextTick` 中傳入的 `callback` 會優先以 **微任務 (microtask)** 的方式執行，如果真的不行最後才會降級成 `setTimeout` 的 **宏任務 (macrotask)**
```js
let timerFunc

if (typeof Promise !== 'undefined' && isNative(Promise)) {
  const p = Promise.resolve()
  timerFunc = () => {
    p.then(flushCallbacks)
    if (isIOS) setTimeout(noop)
  }
  isUsingMicroTask = true
} else if (!isIE && typeof MutationObserver !== 'undefined' && (
  isNative(MutationObserver) ||
  MutationObserver.toString() === '[object MutationObserverConstructor]'
)) {
  let counter = 1
  const observer = new MutationObserver(flushCallbacks)
  const textNode = document.createTextNode(String(counter))
  observer.observe(textNode, {
    characterData: true
  })
  timerFunc = () => {
    counter = (counter + 1) % 2
    textNode.data = String(counter)
  }
  isUsingMicroTask = true
} else if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
  // Fallback to setImmediate.
  // Technically it leverages the (macro) task queue,
  // but it is still a better choice than setTimeout.
  timerFunc = () => {
    setImmediate(flushCallbacks)
  }
} else {
  // Fallback to setTimeout.
  timerFunc = () => {
    setTimeout(flushCallbacks, 0)
  }
}
```

**為什麼優先以微任務方式執行？**
在之前系列文提到每一輪的 **event loop** 會挑出一個 **宏任務 (macrotask)** 執行，接著執行 **微任務佇列 (microtask queue)** 中的所有 **微任務 (microtask)** ，然後再進行 UI 的畫面渲染。
在 `Vue` 中的響應式資料改變，都有可能會修改 DOM 改變畫面，而畫面的改變當然希望是越即時越好，這部分如果使用 `setTimeout` 這種 **宏任務 (macrotask)** 執行 `Vue` 的更新邏輯，每一幀渲染前都只能執行一個 **宏任務 (macrotask)** ，這樣一定很容易遇到[畫面不即時的問題](https://github.com/Ma63d/vue-analysis/issues/6#:~:text=Vue%E4%B8%80%E5%BC%80%E5%A7%8B%E6%9B%BE%E7%BB%8F%E6%94%B9%E8%BF%87nextTick%E7%9A%84%E5%AE%9E%E7%8E%B0%E3%80%82%E6%88%91%E4%BB%AC%E6%9D%A5%E7%9C%8B%E7%9C%8B%E8%BF%99%E4%B8%A4%E4%B8%AAjsFiddle)，所以 `Vue` 在處理資料更新以及 DOM 的修改才優先以 **微任務 (microtask)** 方式執行，這樣在當輪的瀏覽器渲染畫面前資料都已經更新了。


### nextTick 問題解析
##### 1. pending 變數的作用?
```js
const callbacks = []
let pending = false

function flushCallbacks () {
  pending = false
  ...
}

timerFunc = () => {
  Promise.resolve().then(flushCallbacks)
}

export function nextTick (cb?: Function, ctx?: Object) {
  callbacks.push(...)
  if (!pending) {
    pending = true
    timerFunc()
  }
}
```
`pending` 的初始值為 `false`，在一開始使用 `nextTick` 的時候會設為 `true`，然後在 `flushCallbacks` 中 (`callbacks` 佇列全部執行前) 會設為 `false`，這樣可以讓多次 `nextTick` 中加入的 `cb`，在同一次的 **更新時機(nextTick)** 中一次全部執行完

範例：
```js
function cb1() {}
function cb2() {}

nextTick(cb1)
nextTick(cb2)
```
由於有 `pending` 變數的控制，第 5 行執行後 `callbacks = [cb1, cb2]`，但 `timerFunc()` 一樣只會執行一次。接著第 10 行將 `flushCallbacks` 加入 **微任務佇列 (microtask queue)** ，等待之後從 **微任務佇列 (microtask queue)** 挑出 `flushCallbacks` 時，`cb1`, `cb2` 就可以在同一次的 **更新時機(nextTick)** 中一併執行

##### 2. 為什麼需要複製 callbacks 陣列？
```js
function flushCallbacks () {
  pending = false
  const copies = callbacks.slice(0)
  callbacks.length = 0
  for (let i = 0; i < copies.length; i++) {
    copies[i]()
  }
}
```
在實際執行 `callback` 前會先將整個 `callbacks` 陣列複製，原因是當 `nextTick` 中的 `callback` 使用到巢狀的 `nextTick` 時，需要讓父層與子層的 `callback` 在不同次的 **更新時機(nextTick)** 執行

範例：
```js
nextTick(function cb1() {
  nextTick(cb2)
})
```
`cb1` 的子層巢狀用到了 `cb2`，如果不複製 `callbacks` 陣列的話，`cb2` 也會被加入到當輪要執行的 `callbacks` 陣列裡，導致 `cb1` 與 `cb2` 都在同一次的 **更新時機(nextTick)** 中執行，而複製了 `callbacks` 陣列後，`flushCallbacks` 會將這一次該執行完的 `callbacks` 都跑完，而 `cb2` 被加入到的是下一次的 `callbacks` 陣列，也就是在下一次的 **更新時機(nextTick)** 才會執行 

## Reference
- [vue中$nextTick的实现原理](https://jiuto.github.io/jiuto_blog/guide/vue/nextTick.html#vue%E4%B8%AD-nexttick%E7%9A%84%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86)
- [面试官：Vue中的$nextTick有什么作用？](https://vue3js.cn/interview/vue/nexttick.html)
- [Vue源码详解之nextTick：MutationObserver只是浮云，microtask才是核心！](https://github.com/Ma63d/vue-analysis/issues/6)
- [Vue异步更新机制以及$nextTick原理](https://juejin.cn/post/7089980191329484830)

<br />

- [MutationObserver characterData usage without childList](https://stackoverflow.com/questions/33521966/mutationobserver-characterdata-usage-without-childlist)
講解 MutationObserver 中的 characterData 的作用

<br />

- [Understanding setImmediate()](https://nodejs.org/en/learn/asynchronous-work/understanding-setimmediate)
- [The Node.js Event Loop, Timers, and process.nextTick()](https://nodejs.org/en/guides/event-loop-timers-and-nexttick)
- [Event Loop 運行機制解析 - Node.js 篇](https://yu-jack.github.io/2021/03/14/node-event-loop/)
瀏覽器中的 setImmediate 只有已廢棄的 IE 支援，基本上現在 setImmediate 只會在 nodejs 中被使用，以上三篇文章簡介了 Node.js 的 event loop，以及 setImmediate 的執行時機