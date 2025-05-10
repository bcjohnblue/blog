---
title: 理解 Three.js 中 texture 的 offset, repeat 及 wrap
date: 2024-02-18 21:21:13
updated: 2025-05-11 02:06:00
categories: Three.js
tags:
---

## 前言

Threejs 中的 texture(貼圖) 可以設置 offset, repeat 及 wrap 屬性，但大部分文章都只有文字說明而沒有視覺化的範例，所以寫了一個視覺化範例讓自己能夠理解

##### [Demo](https://codesandbox.io/p/devbox/threejs-texture-nds6mp?file=%2Fsrc%2FExperience%2FWorld%2Fobjects%2FPlane.js%3A3%2C21)
圖片渲染在 (x,y) 平面上，右方橘色線為 x 軸，上方綠色線為 y 軸
<img src="/2024/02/18/理解-three-js-中-texture-的-offset-repeat-及-wrap/default.png" width="50%" height="50%">

<!--more-->

## 範例程式碼
```js
import * as THREE from 'three';

export class Plane {
  constructor(experience) {
    this.experience = experience;

    this.create();
    this.createDebugGUI();
  }

  // 創建 PlaneGeometry 並設置 texture
  create() {
    this.geometry = new THREE.PlaneGeometry(8, 8);
    this.loader = new THREE.TextureLoader();
    const texture = this.loader.load(
      'https://cdn.pixabay.com/photo/2018/07/09/17/44/baby-elephant-3526681_1280.png'
    );

    this.material = new THREE.MeshBasicMaterial({ map: texture });
    this.mesh = new THREE.Mesh(this.geometry, this.material);
    this.experience.scene.add(this.mesh);
  }

  // 設置 debug gui
  createDebugGUI() {
    this.experience.debug.gui
      .add(this.material.map.offset, 'x')
      .name('offsetX')
      .min(-1)
      .max(1)
      .step(0.1);
    this.experience.debug.gui
      .add(this.material.map.offset, 'y')
      .name('offsetY')
      .min(-1)
      .max(1)
      .step(0.1);

    this.experience.debug.gui
      .add(this.material.map.repeat, 'x')
      .name('repeatX')
      .min(-10)
      .max(10)
      .step(1);
    this.experience.debug.gui
      .add(this.material.map.repeat, 'y')
      .name('repeatY')
      .min(-10)
      .max(10)
      .step(1);

    this.experience.debug.gui
      .add(this.material.map, 'wrapS')
      .options({
        RepeatWrapping: THREE.RepeatWrapping,
        ClampToEdgeWrapping: THREE.ClampToEdgeWrapping,
        MirroredRepeatWrapping: THREE.MirroredRepeatWrapping
      })
      .onChange(() => {
        this.material.map.needsUpdate = true;
      });
    this.experience.debug.gui
      .add(this.material.map, 'wrapT')
      .options({
        RepeatWrapping: THREE.RepeatWrapping,
        ClampToEdgeWrapping: THREE.ClampToEdgeWrapping,
        MirroredRepeatWrapping: THREE.MirroredRepeatWrapping
      })
      .onChange(() => {
        this.material.map.needsUpdate = true;
      });
  }
}
```

## Offset - 偏移

藉由設置 offset 可以改變 uv 座標對應到的紋理圖片，預設的 offset 為 0

```js
material.map.offset.x = 0;
material.map.offset.y = 0;
```

offset 為 0，原圖位於正中間
<img src="default.png" width="50%" height="50%">

<div style="display: flex; gap: 10px;">
  <div style="flex: 1">
    <div>offset.x 為 -0.5，圖片會往右半邊偏移</div>
    <img src="x_-0.5.png" width="100%">
  </div>
  <div style="flex: 1">
    <div>offset.x 為 0.5，圖片會往左半邊偏移</div>
    <img src="x_0.5.png" width="100%">
  </div>
</div>

<div style="display: flex; gap: 10px; margin-top: 12px;">
  <div style="flex: 1">
    <div>offset.y 為 -0.5，圖片會往上偏移</div>
    <img src="y_-0.5.png" width="100%">
  </div>
  <div style="flex: 1">
    <div>offset.y 為 0.5，圖片會往下偏移</div>
    <img src="y_0.5.png" width="100%">
  </div>
</div>

<br />

這裡我一直有個疑問，為什麼 offset.x 設為 -0.5 的時候圖片是往右半邊偏移而不是往左半邊偏移呢？後來才想出一個合理的解釋，uv 座標的原點在左下角，而 `offset.x = -0.5` 的意思是整個 `uv 座標系.x - 0.5 = 紋理圖片的位置.x`，所以以右上角 uv 座標系的 (1, 1) 來看，x 減掉 0.5 後變成 (0.5, 1) 而這個 (0.5, 1) 對應到的是紋理圖片的位置，所以 offset.x 設為 -0.5 看到的結果就是圖片往右偏移了一半

<img src="https://learn.foundry.com/nuke/content/resources/images/ug_images/uv_coordinates.png">

<img src="uv.png">

[圖片來源 1](https://learn.foundry.com/nuke/content/resources/images/ug_images/uv_coordinates.png)
[圖片來源 2](https://www.cnblogs.com/vadim-web/p/13517244.html)


## Repeat
控制圖片在 uv 方向會 repeat 多少次，預設值為 1
```js
material.map.repeat.x = 1;
material.map.repeat.y = 1;
```

repeat 為 1，原圖不會重複
<img src="default.png" width="50%" height="50%">

repeat.x 跟 repeat.y 都設為 2，所以圖片總共重複 4 次
(另外這裡還設定了 wrap 的方式為 THREE.RepeatWrapping，所以才有辦法重複)
<img src="repeat.png" width="50%" height="50%">

repeat.x 為 -1，設成負數的話圖片會左右顛倒
(另外這裡還設定了 wrapS 的方式為 THREE.RepeatWrapping)
<img src="repeat_minus.png" width="50%" height="50%">

## Wrap
wrap 處理圖片未填滿時，如何渲染剩餘空間的方式，對應到 x, y 軸設定的屬性名稱分別是 wrapS 及 wrapT
```js
material.map.wrapS = THREE.ClampToEdgeWrapping;
material.map.wrapT = THREE.ClampToEdgeWrapping;
```

threejs 中總共有三個值可以做設定：
- THREE.RepeatWrapping: 重複渲染圖片
- THREE.ClampToEdgeWrapping: 最後一個像素點會延伸到邊界 (預設值)
- THREE.MirroredRepeatWrapping: 跟 RepeatWrapping 很像，差別是會鏡像的重複渲染

這三個值乍看之下很抽象，以下我們來看不同範例的演示，會比較清楚

##### THREE.RepeatWrapping
repeat.x 跟 repeat.y 都設為 2，所以圖片總共重複 4 次
<img src="repeat.png" width="50%" height="50%">

##### THREE.ClampToEdgeWrapping
repeat.x 跟 repeat.y 都設為 2，但因為 wrapS 及 wrapT 都設為 THREE.ClampToEdgeWrapping，未填滿的地方會參考最後一個像素點做延伸，而這張大象左邊的耳朵及尾巴分別是最遠的像素點，所以會從這兩點延伸到邊界
<img src="clamp.png" width="50%" height="50%">

##### THREE.MirroredRepeatWrapping
repeat.x 跟 repeat.y 都設為 2，所以圖片總共重複 4 次，但因為是鏡像渲染，所以上下左右的圖片分別是對稱的
<img src="repeat_mirror.png" width="50%" height="50%">

## 版本
- threejs r159

## 參考資料
[ThreeJs入门18-纹理的重复和纹理的回环](https://juejin.cn/post/7062172473726337054)
[three.js UV映射简述](https://www.cnblogs.com/vadim-web/p/13517244.html)
