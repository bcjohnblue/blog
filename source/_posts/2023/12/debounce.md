---
title: debounce 中的 this 指向
date: 2023-12-06 20:42:51
categories: Javascript
tags: 面試題
---

## 什麼是 debounce ?

`debounce` 是一種用於防止事件處理過於頻繁發生的技術，常用於處理使用者輸入，例如按鍵事件或搜尋框的輸入事件。

## 範例

以文字搜尋功能為例，使用者每次搜尋打的文字都會呼叫後端的 api 拿回資料，但通常使用者想搜尋的是最後打字的結果，而此時就適合使用 `debounce` 在一定時間過後才去真正的呼叫後端 api

以下面為例，搜尋框改變時，不會馬上觸發 `onInput` 方法，而是會等 500ms 後，如果搜尋框的文字沒有任何改變，才呼叫 `onInput` 方法

```html
<input type="text" onInput="debounceInput" />
```

```js
const onInput = (event) => {
  console.log(event.target.value);
  // call api...
};
const debounceInput = debounce(onInput, 500);
```

<!--more-->

## 基礎版 - debounce

基礎的 `debounce` 是利用 `setTimeout` 延後 `fn` 函式的執行時間，等到 `delay` 時間過後才會實際呼叫 `fn` 函式執行

```js
function debounce(fn, delay = 500) {
  let timer = null;

  return (...args) => {
    if (timer) clearTimeout(timer);

    timer = setTimeout(() => {
      fn(...args);
    }, delay);
  };
}
```

## 添加 this 版 - debounce

以上的部分是 `debounce` 主要的功用，拿來避免事件頻繁的觸發，但這篇文章想講的是在 `debounce` 中的 `this` 指向

如果查看 `debounce` 相關的教學文章，會發現有些文章的 `debounce` 是這樣寫的：

```js
function debounce(fn, delay = 500) {
  let timer = null;

  return function (...args) {
    const self = this;
    if (timer) clearTimeout(timer);

    timer = setTimeout(function () {
      fn.apply(self, args);
    }, delay);
  };
}
```

當使用 `debounce` 時如果沒有牽涉到 `this` 的指向，不論是 **第一種基礎版** 或是 **第二種添加 this 版** 的寫法都是一樣的，但如果牽扯到 `this` 的使用時就會有所差別

以下我們拿 vue2 的 option api 當作範例，來看看實際使用 `debounce` 時可能會遇到的 `this` 指向問題：

```html
<template>
  <div className="App">
    <input type="text" @input="debounceInput" />
  </div>
</template>
```

```js
<script>
export default {
  mounted() {
    // 寫法 1.
    this.debounceInput = debounce(this.onInput);

    // 寫法 2.
    this.debounceInput = debounce((event) => {
      this.onInput(event);
    });

    // 寫法 3.
    this.debounceInput = debounce(function (event) {
      this.onInput(event);
    });
  },
  methods: {
    onInput(event) {
      const newValue = event.target.value;
    },
  },
}
</script>
```

直接先講結論，**寫法 1.** 及 **寫法 2.** 不論是使用 **基礎版** 或是 **添加 this 版** 的 `debounce`，都是可以正常運作的

但 **寫法 3.** 如果搭配上 **基礎版 - debounce** 時，在 `debounce` 函式中沒有處理 `this` 的指向，`this.onInput` 中的 `this` 等於 `undefined`，因此也就沒有 `onInput` 方法可以呼叫，會直接報錯

```js
// 寫法 3.
this.debounceInput = debounce(function (event) {
  // 這裡的 this 會等於 undefined，導致直接報錯
  this.onInput(event);
});
```

<br />

以下我們來看看這三種寫法，`this` 的指向有什麼樣的差別：

##### 寫法 1.
第一種寫法 `this.onInput` 的 `this` 作用域是在 `mounted` 底下，所以 `this` 指向的是 `vue instance`，完全沒有問題
```js
mounted() {
  this.debounceInput = debounce(this.onInput);
},
```

<br />

##### 寫法 2.
第二種寫法，在 `debounce` 裡使用了箭頭函式
```js
mounted() {
  this.debounceInput = debounce((event) => {
    this.onInput(event);
  });
},
```

> [箭頭函式在宣告它的地方的 this 是什麼，它的 this 就是什麼](https://blog.huli.tw/2019/02/23/javascript-what-is-this/#%E4%B8%8D%E5%90%88%E7%BE%A4%E7%9A%84%E7%AE%AD%E9%A0%AD%E5%87%BD%E5%BC%8F)

箭頭函式沒有自己的 `this`，在箭頭函式中的 `this` 指向就是在宣告它時的 `this`，也就是 `mounted` 中的 `this` 
```js
mounted() {
  console.log('mounted: this', this);
  const arrowFn = (event) => {
    // 這裡的 this 等同於第二行中 mounted 裡的 this
    this.onInput(event);
  };
},
```
所以第 5 行的 `this` 一樣會指向 `vue instance` 沒有問題

<br />

##### 寫法 3.
第三種寫法在 `debounce` 中使用了傳統的 `function`
```js
mounted() {
  this.debounceInput = debounce(function (event) {
    this.onInput(event);
  });
},
```

> [在傳統的 function 執行環境下，this 的指向取決於函數怎麼被呼叫](https://something-about-js-book.onejar99.com/day15#han-shu-zhi-hang-huan-jing-xia-function-context)

所以接下來我們需要先釐清 `debounce` 中的 `fn` 函式如何被呼叫，才能夠知道 `this.onInput` 的 `this` 是指向誰
```js
mounted() {
  const fn = function (event) {
    this.onInput(event);
  };
},
```

這裡我們回頭來看一開始寫的 **基礎版 - debounce**，可以看到 `fn` 在第 8 行被呼叫，而這裡呼叫 `fn` 的時候並沒有傳遞 `this` 的指向，因此在嚴格模式下 `fn` 函式中的 `this` 會是 `undefined`
```js
function debounce(fn, delay = 500) {
  let timer = null;

  return (...args) => {
    if (timer) clearTimeout(timer);

    timer = setTimeout(() => {
      fn(...args);
    }, delay);
  };
}
```

```js
mounted() {
  const fn = function (event) {
    // 這裡的 this 等於 undefined
    this.onInput(event);
  };
  this.debounceInput = debounce(fn);
},
```

Refs.
[簡單呼叫 Callback Function (嚴謹模式)](https://something-about-js-book.onejar99.com/day18#7.2.-jian-chan-hu-jiao-callback-function-yan-jin-mo-shi)

<br />

那麼 **添加 this 版 - debounce** 中的 `this` 指向又是怎麼運作的呢？

在 `debounce` 函式中的第 5 行將 `this` 暫存到 `self` 變數裡，接著又在第 ９ 行利用 `apply` 將 `fn` 函式中的 `this` 指向 `self`，因此最後 `fn` 中 `this` 的指向將等同於第 5 行的 `this`
```js
function debounce(fn, delay = 500) {
  let timer = null;

  return function (...args) {
    const self = this;
    if (timer) clearTimeout(timer);

    timer = setTimeout(function () {
      fn.apply(self, args);
    }, delay);
  };
}
```

而 `debounce` 中第 5 行的 `this` 又是從哪裡來的呢？

這裡我們可以看到呼叫 `debounce` 時指向的 `this` 是由 `this.debounceInput` 中的 `this` 來的，也就是 `vue instance`
```js
mounted() {
  this.debounceInput = debounce(function (event) {
    this.onInput(event);
  });
},
```

##### 小結
`this.debounceInput` 中的 `this` 會傳遞到 `debounce` 函式第 5 行中的 `this`，`this` 賦值給 `self` 後又會經由第 9 行的 `fn.apply` 將 `this` 指向傳遞到 `fn` 中，因此最後 `fn` 中的 `this.onInput` 中的 `this` 也就等同於 `this.debounceInput` 中的 `this`

因此如果丟入 `debounce` 中的 `fn` 函式是採用傳統 `function` 的第三種寫法，這時 `debounce` 函式就必須正確的傳遞 `this` 指向 (**添加 this 版 - debounce**)，才能夠使得 `fn` 函式中的 `this` 正常運作

##### [Demo](https://codesandbox.io/p/devbox/debounce-9q6tlx?file=%2Fsrc%2FApp.vue%3A28%2C11)

## 結論

`debounce` 的教學文章有很多，但一直以來都不太懂為什麼有些文章使用 `fn.apply` 將 `this` 的指向傳入 `fn` 函式裡，而有些沒有。今天藉由 vue2 使用 `debounce` 的寫法，才瞭解傳遞 `this` 的指向是必要的

但現在的 vue3 或是 react16 後流行的 hooks 寫法，都幾乎不太會用到 `this`，因此可以放心不會遇到 **寫法 3.** 的這種 bug 了

## 參考資料
[Day 21：箭頭函數 (Arrow Functions) 的 this 和你想的不一樣 (1)](https://something-about-js-book.onejar99.com/day21)
[淺談 JavaScript 頭號難題 this：絕對不完整，但保證好懂](https://blog.huli.tw/2019/02/23/javascript-what-is-this/)
[What's THIS in JavaScript ? [上]](https://kuro.tw/posts/2017/10/12/What-is-THIS-in-JavaScript-%E4%B8%8A/)
