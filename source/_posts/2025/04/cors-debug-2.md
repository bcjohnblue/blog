---
title: CORS Debug (2) - src 與 crossOrigin 在 img 元素中的順序導致 CORS error
date: 2025-04-17 20:26:00
updated: 2025-05-25 12:31:00
categories: Javascript
tags:
---

###### 此為 **CORS Debug** 系列文章 - 第 2 篇： 

1. <a href="/2025/04/13/cors-debug-1-網頁快取導致的-cors-問題/" target="_blank">CORS Debug (1) - 網頁快取導致的 CORS 問題</a>
2. <a href="/2025/04/17/cors-debug-2-src-與-crossorigin-在-img-元素中的順序導致-cors-error/" target="_blank">CORS Debug (2) - src 與 crossOrigin 在 img 元素中的順序導致 CORS error</a>

## 簡介

在上一篇文章 - <a href="/2025/04/13/cors-debug-1-網頁快取導致的-cors-問題/" target="_blank">網頁快取導致的 CORS 問題</a> 中，最終採用了 `crossOrigin='anonymous'` 這樣的方式解決網頁快取導致的 CORS 錯誤，原本以為這樣已經萬無一失，但沒想到更悲劇的事情發生了 => 在 Safari 瀏覽器有時候會發現網頁上的圖片都不見了

<div style="display: flex; justify-content: center;">
  <img src="/2025/04/17/cors-debug-2-src-與-crossorigin-在-img-元素中的順序導致-cors-error/safari-image-disappear.png" />
</div>

<!--more-->

## 什麼情況下圖片會消失不見

經過一些測試後，發現圖片不見的關鍵因素有以下幾點：

1. 只有 Safari 瀏覽器會出現這個問題，Chrome 瀏覽器正常

2. 第一次從伺服器取得圖片時一定都正常，但重整頁面下一次拿快取圖片的時候**有機率**圖片會消失不見

3. 當圖片消失不見後，即使不斷重整頁面圖片一樣回不來

此時點開 Safari 的 DevTools 來看，又見到了熟悉的 CORS 錯誤

<div style="display: flex; justify-content: center;">
  <img src="./safari-cors-error.png" />
</div>

## CORS 錯誤出現 - 查看網路請求

第一步先看看 CORS 錯誤發生時的網路請求，打開 Safari DevTools 的網路部分會發現奇怪的事，每張圖片都發出了兩次請求，其中一個是黑色沒有錯誤的請求、另一個是紅色的 CORS 錯誤請求

- 沒有錯誤的請求

可以看到來源是**磁碟快取**，但奇怪的地方是明明就已經在 `<img />` 加上 `crossOrigin='anonymous'` 了，但 Response headers 為什麼卻沒有 `Access-Control-Allow-Origin: *` => 這代表之前快取到的是 **非跨域請求** ?

<div style="display: flex; justify-content: center;">
  <img src="./cors-error-request-good.png" />
</div>

- 出現 CORS 錯誤的請求

可以看到 Request headers 帶有 `Origin` 欄位，代表這送出的是 **跨域請求**，而現在出現 CORS 錯誤了，對照上面的結果，看來可以確定之前快取到的是 **非跨域請求**

<div style="display: flex; justify-content: center;">
  <img src="./cors-error-request-bad.png" />
</div>

## 第一次載入圖片 - 查看網路請求

接著我們來看看第一次載入圖片時，是否有可能快取到 **非跨域請求** 呢？

打開 DevTools 看會發現第一次載入圖片時，每張圖片一樣也都發出了兩個請求，其中一個是 **跨域請求** 而另一個是 **非跨域請求**

- 跨域請求

可以看到第一個請求是 **跨域請求**，Request headers 帶有 `Origin` 欄位而 Response headers 也帶有 `Access-Control-Allow-Origin: *`，由於我們在 `img` 元素上帶了 `crossOrigin='anonymous'`，所以發出 **跨域請求** 是合理的

<div style="display: flex; justify-content: center;">
  <img src="./first-request-cors.png" />
</div>

- 非跨域請求

而莫名出現的第二個請求是 **非跨域請求**，Request headers 沒有 `Origin` 欄位，所以 Response headers 也就沒有回傳 `Access-Control-Allow-Origin: *`，這是不合理的

<div style="display: flex; justify-content: center;">
  <img src="./first-request-no-cors.png" />
</div>

## 推測 CORS 錯誤出現的原因

根據以上的觀察，我們知道第一次進入頁面從伺服器獲取圖片時，Safari 不知道為什麼發出了兩個請求，一個是 **跨域請求** 而另一個是 **非跨域請求**，圖片回傳後 Safari 會將圖片快取，但這時候似乎會出現兩個可能性：一個是快取到 **跨域請求** 而另一個是快取到 **非跨域請求**

- 快取到 **跨域請求**

這就沒什麼問題了，之後對於同一張圖片的請求都會覆用 **跨域請求** 的快取

- 快取到 **非跨域請求**

這就是問題的所在，由於一開始快取到 **非跨域請求**，之後對於同一張圖片的請求也都會讀取到 **非跨域請求** 的快取，但因為 `img` 元素加上 `crossOrigin='anonymous'` 代表送出的是 **跨域請求**，而此時 Safari 卻使用到之前快取到的 **非跨域請求**，就導致了 CORS 錯誤再次出現

所以關鍵點就是：為什麼 Safari 瀏覽器對於同一張圖片會送出兩次請求，而且一次是 **跨域請求** 另一次是 **非跨域請求** ？

## Safari `crossOrigin='anonymous'` 擺放的次序是重要的

在經過許久的鬼打牆後，我偶然發現了一篇救命稻草 - [crossorigin and src attribute order matter](https://github.com/sveltejs/svelte/issues/7454)，這篇文章提到 `crossOrigin='anonymous'` 擺放的次序在 Safari 瀏覽器中是很重要的

- `src` 擺在 `crossOrigin='anonymous'` 前面

這種方式就會導致我們上面遇到的那些問題，同一張圖片的請求發出兩次，一次是 **跨域請求** 另一次是 **非跨域請求**，而我們無法控制 Safari 瀏覽器會快取哪個請求，當運氣不好快取到的是 **非跨域請求** 時那就慘了，在快取圖片有效的期間都會出現 CORS 錯誤，因此不管使用者怎麼刷新頁面圖片都是消失不見的

```html
<img
  src="https://bcjohn-cors-static.s3.ap-southeast-2.amazonaws.com/building.jpg"
  crossorigin="anonymous"
  alt="building"
/>
```

- `crossOrigin='anonymous'` 擺在 `src` 前面

實測這種方式 Safari 會運作正常，同一張圖片只會發出一次 **跨域請求**，並正確被快取

```html
<img
  crossorigin="anonymous"
  src="https://bcjohn-cors-static.s3.ap-southeast-2.amazonaws.com/building.jpg"
  alt="building"
/>
```

## 各大前端框架的 issues

除了上面那篇是 `svelte` 發的 issue 外，我發現 `React`, `vue` 的使用者從大約在 2018 年到 今日(2025/04) 都一樣面對著這奇怪的問題

- svelte - [crossorigin and src attribute order matter](https://github.com/sveltejs/svelte/issues/7454)
- react - [crossOrigin attribute needs to be applied before `<img src>` attribute](https://github.com/facebook/react/issues/14035)
- vue - [Safari loads image twice if crossorigin attribute goes after src](https://github.com/vuejs/core/issues/4680)

P.S. 在後面 [研究 React 原始碼的過程中](/2025/04/17/cors-debug-2-src-與-crossorigin-在-img-元素中的順序導致-cors-error/#React-JSX-編譯結果)，我發現 React 其實已經修復此 Safari 特定的 CORS 問題了

## 排除前端框架的影響

雖然看過上面的 issues，把 `crossOrigin='anonymous'` 擺在 `src` 前面避免掉 Safari 瀏覽器這個特有的 CORS 錯誤就沒問題了，但我很好奇這有沒有可能是使用 React, Vue 這些前端框架而導致的結果，如果改為使用純 HTML, JavaScript 會是一樣的嗎？所以我只用了 HTML, JavaScript 寫了幾個範例試試，下面來看看哪些寫法會引起 CORS 錯誤，首先是 Chrome 的部分

#### Chrome 瀏覽器

對於 `crossOrigin='anonymous'` 的順序完全不在乎，各種寫法都正常送出 **跨域請求**，並且 **跨域請求** 回來的結果也都正常被快取，以下只列出其中一個正常的範例寫法

<a href="https://bcjohnblue.github.io/browser-cors-error/chrome-good-1" target="_blank">範例 - chrome-good-1</a>

```html
<script>
  window.onload = function () {
    setTimeout(() => {
      const img = document.createElement('img');
      img.setAttribute(
        'src',
        'https://bcjohn-cors-static.s3.ap-southeast-2.amazonaws.com/building.jpg'
      );
      img.setAttribute('crossOrigin', 'anonymous');
      document.body.appendChild(img);
    }, 0);
  };
</script>
```

#### Safari 瀏覽器

Safari 瀏覽器就真的是莫名其妙了，以下我們來看看：

#### 1. 正常寫法

這是 Safari 唯一一個正常 CORS 的範例，將 `crossorigin="anonymous"` 寫在 `<img />` 元素上可以正常送出 **跨域請求**

<a href="https://bcjohnblue.github.io/browser-cors-error/safari-good-1" target="_blank">範例 - safari-good-1</a>

```html
<body>
  <img
    src="https://bcjohn-cors-static.s3.ap-southeast-2.amazonaws.com/building.jpg"
    crossorigin="anonymous"
  />
</body>
```

稍微提及一下，這裡的寫法跟我們一開始使用前端框架(React, Vue)的寫法是不一樣的，在前端框架裡我們寫的是類似 [JSX](https://www.explainthis.io/zh-hant/swe/what-is-jsx) 的語法，而這些 JSX 最終編譯成 HTML 後，不一定會長得跟這裡的寫法一樣，至於 React, Vue 將 JSX 編譯後的 HTML 會長什麼樣子，我們在後面會提到

#### 2. 不好的寫法

以下兩種寫法，同一張圖片都會送出兩次請求 - **跨域請求** 與 **非跨域請求**，但請求完後的圖片**不會**被瀏覽器快取

為什麼我認為是不好的寫法？發出兩次請求很怪，更怪的是竟然不會被快取起來，這種寫法主要是性能不太好，因為每張圖片都會去伺服器要求資源，而且之後也都無法覆用快取，但好處是因為每次請求後都不會儲存快取，所以不會導致 CORS 問題進而讓圖片消失不見 

<a href="https://bcjohnblue.github.io/browser-cors-error/safari-bad-1" target="_blank">範例 - safari-bad-1</a>

```html
<script>
  window.onload = function () {
    const img = document.createElement('img');
    img.src =
      'https://bcjohn-cors-static.s3.ap-southeast-2.amazonaws.com/building.jpg';
    img.crossOrigin = 'anonymous';
    document.body.appendChild(img);
  };
</script>
```

<a href="https://bcjohnblue.github.io/browser-cors-error/safari-bad-2" target="_blank">範例 - safari-bad-2</a>

```html
<script>
  window.onload = function () {
    const img = document.createElement('img');
    img.setAttribute(
      'src',
      'https://bcjohn-cors-static.s3.ap-southeast-2.amazonaws.com/building.jpg'
    );
    img.setAttribute('crossOrigin', 'anonymous');
    document.body.appendChild(img);
  };
</script>
```

#### 3. 非常糟的寫法

以下兩種寫法，同一張圖片都會送出兩次請求 - **跨域請求** 與 **非跨域請求**，而請求完後的圖片**會**被瀏覽器快取

為什麼我認為這是非常糟的寫法呢？原因是這個寫法會被瀏覽器快取啊，而且還無法確定被快取的是 **跨域請求** 或是 **非跨域請求**，當快取到的是 **非跨域請求** 時就會遇到我們開頭的那個圖片消失不見的問題

<a href="https://bcjohnblue.github.io/browser-cors-error/safari-very-bad-1" target="_blank">範例 - safari-very-bad-1</a>

```html
<script>
  window.onload = function () {
    setTimeout(() => {
      const img = document.createElement('img');
      img.src =
        'https://bcjohn-cors-static.s3.ap-southeast-2.amazonaws.com/building.jpg';
      img.crossOrigin = 'anonymous';
      document.body.appendChild(img);
    }, 0);
  };
</script>
```

<a href="https://bcjohnblue.github.io/browser-cors-error/safari-very-bad-2" target="_blank">範例 - safari-very-bad-2</a>

```html
<script>
  window.onload = function () {
    setTimeout(() => {
      const img = document.createElement('img');
      img.setAttribute(
        'src',
        'https://bcjohn-cors-static.s3.ap-southeast-2.amazonaws.com/building.jpg'
      );
      img.setAttribute('crossOrigin', 'anonymous');
      document.body.appendChild(img);
    }, 0);
  };
</script>
```

#### 4. 非常好的寫法

只是想強調這個寫法跟上面 [3. 非常糟的寫法](/2025/04/17/cors-debug-系列文-2-crossorigin-anonymous-的順序導致-cors-error/#3-非常糟的寫法) 很類似，差別只在把 `crossOrigin='anonymous'` 的順序提前到 `src` 之前，但結果會跟 [1. 正常寫法](/2025/04/17/cors-debug-系列文-2-crossorigin-anonymous-的順序導致-cors-error/#1-正常寫法) 一樣，每張圖片只會送出一個 **跨域請求**，並且請求完後的圖片也**會**被快取

<a href="https://bcjohnblue.github.io/browser-cors-error/safari-very-good-1" target="_blank">範例 - safari-very-good-1</a>

```html
<script>
  window.onload = function () {
    setTimeout(() => {
      const img = document.createElement('img');
      img.crossOrigin = 'anonymous';
      img.src =
        'https://bcjohn-cors-static.s3.ap-southeast-2.amazonaws.com/building.jpg';
      document.body.appendChild(img);
    }, 0);
  };
</script>
```

<a href="https://bcjohnblue.github.io/browser-cors-error/safari-very-good-2" target="_blank">範例 - safari-very-good-2</a>

```html
<script>
  window.onload = function () {
    setTimeout(() => {
      const img = document.createElement('img');
      img.setAttribute('crossOrigin', 'anonymous');
      img.setAttribute(
        'src',
        'https://bcjohn-cors-static.s3.ap-southeast-2.amazonaws.com/building.jpg'
      );
      document.body.appendChild(img);
    }, 0);
  };
</script>
```

## 前端框架對應到的是哪種寫法？

上面分析完純 HTML, Javascript 的幾種寫法最終如何影響請求以及快取的方式，接著讓我們回頭來看，現今大家常用的前端框架 JSX 的語法最終編譯出來後對應到的是哪種寫法

#### React or Vue 中類 JSX 的寫法

```html
<img
  src="https://bcjohn-cors-static.s3.ap-southeast-2.amazonaws.com/building.jpg"
  crossorigin="anonymous"
  alt="building"
/>
```

#### - React JSX 編譯結果

以上 React JSX 的寫法會藉由 babel 編譯成 [React.createElement](https://react.dev/reference/react/createElement) 的形式：

```js
React.createElement('img', {
  src: 'https://bcjohn-cors-static.s3.ap-southeast-2.amazonaws.com/building.jpg',
  crossOrigin: 'anonymous',
  alt: 'building'
});
```

而根據 [React 源码解析 (四) —— Fiber 树初次构造](https://juejin.cn/post/7431122270641094675) 這篇文章的分析，元素在賦予 `props` 屬性時會執行到 [finalizeInitialChildren](https://github.com/facebook/react/blob/bc6184dd993e6ea0efdee7553293676db774c3ca/packages/react-dom-bindings/src/client/ReactFiberConfigDOM.js#L584) 函式，而其中的 [setInitialProperties](https://github.com/facebook/react/blob/bc6184dd993e6ea0efdee7553293676db774c3ca/packages/react-dom-bindings/src/client/ReactDOMComponent.js#L1045) 就處理了 `img` 元素賦予 `props` 的邏輯：

```js
switch (tag) {
  // img tags previously were implemented as void elements with non delegated events however Safari (and possibly Firefox)
  // begin fetching the image as soon as the `src` or `srcSet` property is set and if we set these before other properties
  // that can modify the request (such as crossorigin) or the resource fetch (such as sizes) then the browser will load
  // the wrong thing or load more than one thing. This implementation ensures src and srcSet are set on the instance last
  case 'img': {
    listenToNonDelegatedEvent('error', domElement);
    listenToNonDelegatedEvent('load', domElement);
    // Mostly a port of Void Element logic with special casing to ensure srcset and src are set last
    let hasSrc = false;
    let hasSrcSet = false;
    for (const propKey in props) {
      if (!props.hasOwnProperty(propKey)) {
        continue;
      }
      const propValue = props[propKey];
      if (propValue == null) {
        continue;
      }
      switch (propKey) {
        case 'src':
          hasSrc = true;
          break;
        case 'srcSet':
          hasSrcSet = true;
          break;
        case 'children':
        case 'dangerouslySetInnerHTML': {
          // TODO: Can we make this a DEV warning to avoid this deny list?
          throw new Error(
            `${tag} is a void element tag and must neither have \`children\` nor ` +
              'use `dangerouslySetInnerHTML`.'
          );
        }
        // defaultChecked and defaultValue are ignored by setProp
        default: {
          setProp(domElement, tag, propKey, propValue, props, null);
        }
      }
    }
    if (hasSrcSet) {
      setProp(domElement, tag, 'srcSet', props.srcSet, props, null);
    }
    if (hasSrc) {
      setProp(domElement, tag, 'src', props.src, props, null);
    }
    return;
  }
}
```

wait 等等！這裡看起來不太正常，為什麼在 `case 'img'` 上面的註解提到了 Safari crossorigin 的順序問題，我一直以為根據這篇 [crossOrigin attribute needs to be applied before <img src> attribute](https://github.com/facebook/react/issues/14035) 最後的留言在 2021 年 9 月，這個順序的 bug 在 React 中應該還是存在：

<div style="display: flex; justify-content: center;">
  <img src="./react-issue.png" />
</div>

但從 React 原始碼中才發現原來這個 Safari 特定的 bug 已經被修復了啊！後來利用 git blame 找到了修改的 PR - [[Fiber] Ensure srcset and src are assigned last on img instances](https://github.com/facebook/react/pull/30340) 是在 2024 年 7 月，從 React releases 可以發現對應到的是 [19.0.0 版本](https://github.com/facebook/react/releases/tag/v19.0.0)

這時趕快把我在上一篇文章用 React 所寫的範例，版本用的是 React 18.3.1，切換成 19.0.0 後，哇！發現 React 編譯後真的把 `crossOrigin` 移到 `src` 前面了！

#### - Vue JSX 編譯結果

Vue 的類 JSX 語法叫做 [模板语法](https://cn.vuejs.org/guide/essentials/template-syntax.html)，之後會藉由 [h](https://cn.vuejs.org/guide/extras/render-function) 函式轉化爲 [vnode](https://cn.vuejs.org/guide/extras/rendering-mechanism#virtual-dom)

```js
import { h } from 'vue';

const vnode = h('img', {
  src: 'https://bcjohn-cors-static.s3.ap-southeast-2.amazonaws.com/building.jpg',
  crossorigin: 'anonymous',
  alt: 'building'
});
```

根據 [Vue3 源码解析之 render（一）](https://juejin.cn/post/7312353149813342249)，`vnode` 會傳入 [mountElement](https://github.com/vuejs/core/blob/4f792535e25af6941d1eb267fe16e4121623a006/packages/runtime-core/src/renderer.ts#L633) 函式，其中的 [hostPatchProp](https://github.com/vuejs/core/blob/4f792535e25af6941d1eb267fe16e4121623a006/packages/runtime-core/src/renderer.ts#L680) 負責處理傳入的 `props` 屬性，而 `hostPatchProp` 的函式實際上使用的是 `packages/runtime-dom/src/patchProp.ts` 檔案中的 [patchProp](https://github.com/vuejs/core/blob/4f792535e25af6941d1eb267fe16e4121623a006/packages/runtime-dom/src/patchProp.ts#L25) 函式

```js
export const patchProp: DOMRendererOptions['patchProp'] = (
  el,
  key,
  prevValue,
  nextValue,
  namespace,
  parentComponent,
) => {
  const isSVG = namespace === 'svg'
  if (key === 'class') {
    patchClass(el, nextValue, isSVG)
  } else if (key === 'style') {
    patchStyle(el, prevValue, nextValue)
  } else if (isOn(key)) {
    // ignore v-model listeners
    if (!isModelListener(key)) {
      patchEvent(el, key, prevValue, nextValue, parentComponent)
    }
  } else if (
    key[0] === '.'
      ? ((key = key.slice(1)), true)
      : key[0] === '^'
        ? ((key = key.slice(1)), false)
        : shouldSetAsProp(el, key, nextValue, isSVG)
  ) {
    patchDOMProp(el, key, nextValue, parentComponent)
    // #6007 also set form state as attributes so they work with
    // <input type="reset"> or libs / extensions that expect attributes
    // #11163 custom elements may use value as an prop and set it as object
    if (
      !el.tagName.includes('-') &&
      (key === 'value' || key === 'checked' || key === 'selected')
    ) {
      patchAttr(el, key, nextValue, isSVG, parentComponent, key !== 'value')
    }
  } else if (
    // #11081 force set props for possible async custom element
    (el as VueElement)._isVueCE &&
    (/[A-Z]/.test(key) || !isString(nextValue))
  ) {
    patchDOMProp(el, camelize(key), nextValue, parentComponent, key)
  } else {
    // special case for <input v-model type="checkbox"> with
    // :true-value & :false-value
    // store value as dom properties since non-string values will be
    // stringified.
    if (key === 'true-value') {
      ;(el as any)._trueValue = nextValue
    } else if (key === 'false-value') {
      ;(el as any)._falseValue = nextValue
    }
    patchAttr(el, key, nextValue, isSVG, parentComponent)
  }
}
```

從 `patchProp` 函式中的註解發現原來的底層程式碼對 `props` 做了各種處理，但都與我們想找的 `img` 元素無關，看起來 `img` 元素最終會走到第 52 行的 `patchAttr`，而 `patchAttr` 是從 `packages/runtime-dom/src/modules/attrs.ts` 檔案裡來的

[patchAttr](https://github.com/vuejs/core/blob/4f792535e25af6941d1eb267fe16e4121623a006/packages/runtime-dom/src/modules/attrs.ts#L16)

```js
export function patchAttr(
  el: Element,
  key: string,
  value: any,
  isSVG: boolean,
  instance?: ComponentInternalInstance | null,
  isBoolean: boolean = isSpecialBooleanAttr(key)
): void {
  if (isSVG && key.startsWith('xlink:')) {
    if (value == null) {
      el.removeAttributeNS(xlinkNS, key.slice(6, key.length));
    } else {
      el.setAttributeNS(xlinkNS, key, value);
    }
  } else {
    if (__COMPAT__ && compatCoerceAttr(el, key, value, instance)) {
      return;
    }

    // note we are only checking boolean attributes that don't have a
    // corresponding dom prop of the same name here.
    if (value == null || (isBoolean && !includeBooleanAttr(value))) {
      el.removeAttribute(key);
    } else {
      // attribute value is a string https://html.spec.whatwg.org/multipage/dom.html#attributes
      el.setAttribute(
        key,
        isBoolean ? '' : isSymbol(value) ? String(value) : value
      );
    }
  }
}
```

這個 `patchAttr` 函式看起來是最後賦予屬性的地方了，所以在 vue 中 `img` 的模板語法：

```html
<img
  src="https://bcjohn-cors-static.s3.ap-southeast-2.amazonaws.com/building.jpg"
  crossorigin="anonymous"
  alt="building"
/>
```

最終在 `patchAttr` 函式中會將 `props` 屬性用 [setAttribute](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/setAttribute) 的方式設置在 `img` 元素上，如同這樣的寫法：

```js
const img = document.createElement('img');
img.setAttribute(
  'src',
  'https://bcjohn-cors-static.s3.ap-southeast-2.amazonaws.com/building.jpg'
);
img.setAttribute('crossOrigin', 'anonymous');
```

#### 前端框架寫法總結

在研究 React 原始碼的過程中意外發現原來 19.0.0 版本就特別為 `img` 元素 `crossorigin` 的順序做了特殊處理，所以目前的 React 即使你把 `crossorigin` 放在 `src` 前面也不會遇到我們今天這篇文章 Safari 圖片消失不見的問題

但 Vue 中並沒有對 `img` 元素進行特殊處理，最終是以 `setAttribute` 的方式將 `props` 依序設置在 `img` 元素上，而這種方式就如同 <a href="/2025/04/17/cors-debug-2-src-與-crossorigin-在-img-元素中的順序導致-cors-error/#3-非常糟的寫法" target="_blank">3. 非常糟的寫法</a>，最終出現 Safari 圖片偶然消失不見的問題

## 後續

我在 [webkit bug report](https://bugs.webkit.org/) 的網頁上似乎找不到之前有回報類似的問題，根據各大前端框架 issues 回報問題的時間點來看，最早從 2018 年問題就存在了，但到今日 Safari 都沒有進行修復，雖然感覺回報 bug 也很難等到修復的那一天，但反正都花時間研究這麼多了，乾脆送個 [bug 單回報](https://bugs.webkit.org/show_bug.cgi?id=291793) 看之後有沒有進一步的消息了

## 心得

在一開始看到 [crossorigin and src attribute order matter](https://github.com/sveltejs/svelte/issues/7454) 這篇 issue 時，其實我是非常驚訝的，第一次知道 HTML attribute 的順序原來是有影響的，好奇大家是不是也都曾經知道或遇過這件事呢？

而且以我們這篇文章遇到的問題來說，我覺得是非常嚴重的，如果不知道順序會有影響，而將 `src` 屬性放在 `crossorigin` 前，由於 Safari 有機率快取到 **非跨域請求** 所以導致圖片消失不見，重點是我在測試的過程中覺得這個機率也不低，感覺至少有 10% 的機率會看不到圖片

另外一方面當圖片不見發生時，即使使用者不斷重整畫面，但只要快取沒有過期圖片就會一直出現 CORS 錯誤拿不到，而且通常圖片快取的時間都會設的比較長一個禮拜、一個月或甚至是一年，在這麼長的時間裡圖片永遠看不到會讓人覺得這是一個壞掉的網站

## 延伸閱讀

#### 1. 如何清除快取？

由於這篇文章有很多部分的操作都需要保證清完瀏覽器的快取，所以這邊稍微簡介一下我在 Chrome 跟 Safari 清除快取的方式

- Chrome

按下 F12 打開 DevTools => 在網頁重整鍵上方按下滑鼠右鍵 => 選擇 **清除快取並強制重新載入**

<div style="display: flex; justify-content: center; margin-top: -1.2em">
  <img src="./chrome-clear-cache.png" />
</div>

- Safari

將 **網頁開發者功能** 開啟 => 點擊開發 Tab => 點擊 **清除快取資料**

<div style="display: flex; justify-content: center;">
  <img src="./safari-clear-cache.png" />
</div>

#### 2. Chrome 瀏覽器為什麼不會出現 CORS 錯誤？

同樣的一個寫法在 Safari 會導致 CORS 錯誤但為什麼 Chrome 是正常的呢？

```html
<script>
  window.onload = function () {
    setTimeout(() => {
      const img = document.createElement('img');
      img.setAttribute(
        'src',
        'https://bcjohn-cors-static.s3.ap-southeast-2.amazonaws.com/building.jpg'
      );
      img.setAttribute('crossOrigin', 'anonymous');
      document.body.appendChild(img);
    }, 0);
  };
</script>
```

我有找到 chromium 曾經發出的一個 PR - [Image Loading: `crossorigin` mutations should queue a microtask](https://chromium-review.googlesource.com/c/chromium/src/+/2102992)，裡面提供了一個 doc 檔案 - [Image Loading: Relevant Mutations](https://docs.google.com/document/d/1Xp34FIbbZnJILl0PNd1sfs_3z1HSriUVyRUgnAesGZk/edit?tab=t.0#heading=h.1amk7w8s8dxx) 似乎對這部分有解釋，以下為原文摘要：

##### Context

As per the HTML Standard, an image element has a set of relevant mutations. When a relevant mutation occurs, the standard's #updating-the-image-data algorithm is fired. This algorithm, at a high-level, does:

1. Some basic processing of an image's properties: ImageLoader::UpdateFromElement
2. Queues a microtask to continue the rest of the image fetching process: ImageLoader::EnqueueImageLoadingMicroTask
3. Continues the image loading process, where the resolved URL is parsed, the request is configured and made: ImageLoader::DoUpdateFromElement.

<div style="display: flex; justify-content: center;">
  <img src="./doc-1.png" />
</div>

##### 解釋 - Context

`img` 元素在 spec 裡有定義一個叫做 [relevant mutations](https://html.spec.whatwg.org/multipage/images.html#relevant-mutations) 的東西，當觸發 **relevant mutations** 時，就會執行一個叫做 **#updating-the-image-data** 的算法

那什麼時候會觸發 **relevant mutations** 呢？ Spec 裡有寫到當 `src` 被設置、改變、移除時或是 `crossorigin` 屬性改變的時候都會引起 **relevant mutations**

當 `relevant mutations` 被觸發後，接著就會執行 **#updating-the-image-data** 的算法，而這個算法對應到 Spec 裡的規範 - [4.8.4.3.5 Updating the image data](https://html.spec.whatwg.org/multipage/images.html#updating-the-image-data)，點進去看會發現總共有 Step 1 一直到 Step 27 這麼多步驟

這麼多的步驟實在令人眼花撩亂，我稍微看完後認為最重要的是 Step 8 及 Step 9

> 8. Queue a microtask to perform the rest of this algorithm, allowing the task that invoked this algorithm to continue.

Step 8 表明了，從 Step 9 開始一直到最後的 Step 27 都會由一個算法執行，而這個算法對應到的是 `DoUpdateFromElement()`，但 `DoUpdateFromElement()` 並不是馬上執行的喔，而是放在 [microtask 的 queue](https://zh.javascript.info/microtask-queue) 裡等待之後再執行

> 9. If another instance of this algorithm for this img element was started after this instance (even if it aborted and is no longer running), then return.

接著 Step 9 說如果同一張圖片又執行了一次這個 **#updating-the-image-data** 的算法，那步驟 9 之後要處理的邏輯就都捨棄 return 掉囉

我想了一下覺得 **Context** 這個段落要表達的意思應該是這樣的，假設現在有一段程式寫成這樣：

```js
const img = new Image();
img.src = '...';
img.crossOrigin = 'anonymous';
```

當執行到第二行 `img.src = "..."` 時，因為 `src` 被指定了某個 url，會觸發 **relevant mutations** 並接著執行 **#updating-the-image-data** 算法，而執行到 Step 8 時會將 Step 9 到最後 Step 27 該執行的邏輯，都先放在一個 **microtask queue** 裡等待之後執行

接著執行到第三行 `img.crossOrigin = 'anonymous'`，因為 `crossOrigin` 的值改變了，觸發 **relevant mutations**，又執行了 **#updating-the-image-data** 算法，當執行到 Step 8 時又創建了一個 **microtask** 並放在 **queue** 裡等待之後執行

**queue** 的執行是先進先出的，所以第二行 `img.src = "..."` 的這個 **microtask** 會先被執行，此時執行到的是 Step 9，發現對同一個 `img` 元素來說**後面**有另外一個由 `img.crossOrigin = 'anonymous'` 所創建的 **microtask**，因此 `img.src = "..."` 的 **microtask** 就只會執行到 Step 9 就 return 掉了

下一個執行到的是由 `img.crossOrigin = 'anonymous'` 所創建的 **microtask**，此時執行到的是 Step 9，發現後面已經沒有同一個算法所創建的 **microtask** 了，所以接著就是一路執行到最後的 Step 27

可以發現這個 **microtask queue** 的過程中，前面被加入 **queue** 的 **microtask** 都會在 Step 9 被 return 掉，只有最後加入的 **microtask** 會繼續往下走到 Step 26 - Fetch the image 實際發出請求，而此時也因為知道 `img` 元素上有屬性 `crossOrigin` 了，所以可以正確的發出 **跨域請求**，這對應到 Spec 規範中特別 highlight 的一段話

<div style="display: flex; justify-content: center;">
  <img src="./sepc-avoid-multiple-request.png" />
</div>

<br />

藉由這種 **microtask queue** 的方式，可以將 `img` 元素的屬性都收集起來，並且最後只發出一個網路請求

## 附錄

1. 範例 Repo 放在此 - [browser-cors-error](https://github.com/bcjohnblue/browser-cors-error)，有興趣的人可以自己試看看

2. 本篇文章測試的瀏覽器為：

- Chrome 135
- Safari 18.3

3. 本篇文章分析前端框架原始碼的版本為：

- React 19.1.0

- Vue 3.5.13

## 參考資料

[React 源码解析-虚拟 DOM](https://juejin.cn/post/6965462394286014471)
[React 源码分析 1-jsx 转换及 React.createElement](https://juejin.cn/post/7177517711486877756)
[Vue3 源码解析之 runtime](https://juejin.cn/post/7308289054029250599)
