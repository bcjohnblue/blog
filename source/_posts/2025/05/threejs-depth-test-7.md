---
title: Three.js 中物體的遠近關係 (7) - 使用 polygonOffset 動態改變深度值
date: 2025-05-29 23:53:04
updated: 2025-05-29 23:53:04
categories: Three.js
mathjax: true
---

###### 此為 **Three.js 中物體的遠近關係** 系列文章 - 第 7 篇：

1. <a href="/2025/02/13/three-js-中物體的遠近關係-1-什麼是深度測試？/" target="_blank">Three.js 中物體的遠近關係 (1) - 什麼是深度測試？</a>
2. <a href="/2025/02/28/three-js-中物體的遠近關係-2-左手-右手座標系與齊次座標/" target="_blank">Three.js 中物體的遠近關係 (2) - 左手/右手座標系與齊次座標</a>
3. <a href="/2025/02/28/three-js-中物體的遠近關係-3-深度值的計算方式/" target="_blank">Three.js 中物體的遠近關係 (3) - 深度值的計算方式</a>
4. <a href="/2025/03/17/three-js-中物體的遠近關係-4-對數深度值/" target="_blank">Three.js 中物體的遠近關係 (4) - 對數深度值</a>
5. <a href="/2025/03/31/three-js-中物體的遠近關係-5-渲染物體的順序/" target="_blank">Three.js 中物體的遠近關係 (5) - 渲染物體的順序</a>
6. <a href="/2025/05/04/three-js-中物體的遠近關係-6-如何正確的渲染透明粒子？/" target="_blank">Three.js 中物體的遠近關係 (6) - 如何正確的渲染透明粒子？</a>
7. <a href="/2025/05/29/three-js-中物體的遠近關係-7-使用-polygonoffset-動態改變深度值/" target="_blank">Three.js 中物體的遠近關係 (7) - 使用 polygonOffset 動態改變深度值</a>

## 前言

在第四篇系列文 - <a href="/2025/03/17/three-js-中物體的遠近關係-4-對數深度值/#大尺度場景下導致的-z-fighting" target="_blank">大尺度場景下導致的 z-fighting</a> 中提到當兩個物體的深度值非常接近時，會難以辨別是哪個物體在前、哪個物體在後造成 z-fighting 問題，今天這篇文章則是希望使用 [polygonOffset](https://threejs.org/docs/#api/zh/materials/Material.polygonOffset) 來改變物體的深度值以解決 z-fighting 的問題

<!--more-->

## polygonOffset 的用法

```js
const material = new THREE.Material({
  polygonOffset: true,
  polygonOffsetFactor: -0.1,
  polygonOffsetUnits: -1
});
```

**polygonOffset**: 開啟後可以利用 [polygonOffsetFactor](https://threejs.org/docs/#api/zh/materials/Material.polygonOffsetFactor) 及 [polygonOffsetUnits](https://threejs.org/docs/#api/zh/materials/Material.polygonOffsetUnits) 調整深度值

**polygonOffsetFactor**: 調整 `polygonOffset` 偏移的比例

**polygonOffsetUnits**: 調整 `polygonOffset` 偏移的大小

## polygonOffset 的計算公式

$$
offset = m * factor + r * unit
$$

**offset**: 最終改變的深度偏移值

**m**: **深度坡度(depth slope)**，代表著 z 軸深度值除以 x, y 軸的變化量，後面會再提到

**factor**: `polygonOffsetFactor` 設定的值

**r**: 是一個常數，代表深度值的最小單位，實際的值取決於 <a href="/2025/03/17/three-js-中物體的遠近關係-4-對數深度值/#深度值的精度" target="_blank">深度值的精度</a>

**unit**: `polygonOffsetUnits` 設定的值

所以當希望一個物體離我們比較近的時候可以設定 `polygonOffsetFactor` 及 `polygonOffsetUnits` 都是負數，這樣可以將深度值變小代表**物體離鏡頭更近**，反過來則是將 `polygonOffsetFactor` 及 `polygonOffsetUnits` 設成正數，代表**物體離鏡頭更遠**

## 範例 - 應用 polygonOffset

[範例 Repo](https://github.com/bcjohnblue/threejs-polygonOffset)

[線上展示](https://bcjohnblue.github.io/threejs-polygonOffset/)

在這個範例中我們嘗試將自己的 logo 貼到車子的前面，其中 logo 跟車頭燈所在的那片平面，他們的 **z** 值都是一樣的，所以當沒有設定 `polygonOffset` 前，可以看到 logo 出現明顯的 z-fighting 問題

<video src="https://bcjohn-public-static.s3.ap-southeast-2.amazonaws.com/polygon-offset-z-fighting.mp4" autoplay muted loop style="width: 100%"></video>

此時將 `polygonOffset` 打開，因為希望 logo 可以顯示在前面，所以將 `polygonOffsetFactor` 及 `polygonOffsetUnits` 都設成負數使 logo 的深度值變小，可以簡單的設定 `polygonOffsetFactor = -0.1` 及 `polygonOffsetUnits = -1`，如此一來就可以看到 logo 正常顯示不會一直閃爍

<video src="https://bcjohn-public-static.s3.ap-southeast-2.amazonaws.com/polygon-offset-turn-on.mp4" autoplay muted loop style="width: 100%"></video>

#### 深度坡度分析

**深度坡度(depth slope)** 代表 z 軸深度值除以 x, y 軸的變化量，這意味著 **深度坡度** 的值是浮動的，當物體垂直於視線方向的鏡頭時，即使 x, y 軸的座標改變，z 軸的深度值都不會改變，也就是說此時 **深度坡度** 的值接近於 0，而當物體幾乎平行於鏡頭時，x, y 軸的座標只要變一點，z 軸的深度值就會急劇的改變代表此時 **深度坡度** 的值非常大

以下面的影片展示為例，藍色的直線指向 +z 軸，當設定 `polygonOffsetFactor = -0.1` 及 `polygonOffsetUnits = 3` 並且 logo 所在的平面幾乎垂直於鏡頭時，由於 **深度坡度** 的值很小，因此最後計算出來的深度值幾乎都是受到 `polygonOffsetUnits` 的影響，造成 logo 的深度值變大而隱藏在藍色車子裡面，但當將鏡頭往上或往下移動時，**深度坡度** 的值就會變大，因此 `polygonOffsetFactor` 會大於 `polygonOffsetUnits` 造成的影響，就可以看到 logo 顯示在前面

<video src="https://bcjohn-public-static.s3.ap-southeast-2.amazonaws.com/polygon-offset-z-direction.mp4" autoplay muted loop style="width: 100%"></video>

## 參考資料

[Polygon Offset](https://www.zeuscmd.com/tutorials/opengl/15-PolygonOffset.php)