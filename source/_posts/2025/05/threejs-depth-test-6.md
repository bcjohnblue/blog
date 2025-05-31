---
title: Three.js 中物體的遠近關係 (6) - 如何正確的渲染透明粒子？
date: 2025-05-04 09:53:56
updated: 2025-05-11 02:06:00
categories: Three.js
tags:
---

###### 此為 **Three.js 中物體的遠近關係** 系列文章 - 第 6 篇：

1. <a href="/2025/02/13/three-js-中物體的遠近關係-1-什麼是深度測試？/" target="_blank">Three.js 中物體的遠近關係 (1) - 什麼是深度測試？</a>
2. <a href="/2025/02/28/three-js-中物體的遠近關係-2-左手-右手座標系與齊次座標/" target="_blank">Three.js 中物體的遠近關係 (2) - 左手/右手座標系與齊次座標</a>
3. <a href="/2025/02/28/three-js-中物體的遠近關係-3-深度值的計算方式/" target="_blank">Three.js 中物體的遠近關係 (3) - 深度值的計算方式</a>
4. <a href="/2025/03/17/three-js-中物體的遠近關係-4-對數深度值/" target="_blank">Three.js 中物體的遠近關係 (4) - 對數深度值</a>
5. <a href="/2025/03/31/three-js-中物體的遠近關係-5-渲染物體的順序/" target="_blank">Three.js 中物體的遠近關係 (5) - 渲染物體的順序</a>
6. <a href="/2025/05/04/three-js-中物體的遠近關係-6-如何正確的渲染透明粒子？/" target="_blank">Three.js 中物體的遠近關係 (6) - 如何正確的渲染透明粒子？</a>
7. <a href="/2025/05/29/three-js-中物體的遠近關係-7-使用-polygonoffset-動態改變深度值/" target="_blank">Three.js 中物體的遠近關係 (7) - 使用 polygonOffset 動態改變深度值</a>

## 前言

前五篇的系列文章讓我們理解 Three.js 中物體的遠近關係是如何決定的，今天這篇文章藉由 [Three.js journey - lesson particles](https://threejs-journey.com/) 這門課程中渲染粒子系統的範例，學習要怎麼正確的渲染粒子使其前後呈現的順序是正確的

<!--more-->

## 渲染粒子系統

這裡先貼上粒子系統的範例程式碼 - [threejs-particles](https://github.com/bcjohnblue/threejs-particles)，下面的這段程式碼會生成 5000 個透明的粒子顯示在畫面上

```js
export class ParticleSystem {
  constructor(experience) {
    this.experience = experience;
    this.scene = experience.scene;
    this.count = 5000;

    this.setGeometry();
    this.setMaterial();
    this.setPoints();
  }

  setGeometry() {
    this.geometry = new THREE.BufferGeometry();
    const positions = new Float32Array(this.count * 3);

    for (let i = 0; i < this.count * 3; i++) {
      positions[i] = (Math.random() - 0.5) * 10;
    }

    this.geometry.setAttribute(
      'position',
      new THREE.BufferAttribute(positions, 3)
    );
  }

  setMaterial() {
    const textureLoader = new THREE.TextureLoader();
    const particleTexture = textureLoader.load('/textures/particles/2.png');

    this.material = new THREE.PointsMaterial({
      size: 0.1,
      sizeAttenuation: true,
      color: new THREE.Color('#ff88cc'),
      transparent: true,
      alphaMap: particleTexture
    });
  }

  setPoints() {
    this.points = new THREE.Points(this.geometry, this.material);
    this.scene.add(this.points);
  }
}
```

- `setGeometry`

使用 [BufferGeometry](https://threejs.org/docs/?q=BufferGeometry#api/zh/core/BufferGeometry) 的方式創造 5000 個點的座標

- `setMaterial`

使用 [PointsMaterial](https://threejs.org/docs/?q=PointsMaterial#api/zh/materials/PointsMaterial) 創建粒子的材質，將 `transparent` 設為 `true`，代表粒子是透明的可以看到後面的其他粒子，另外也使用了 [alphaMap](https://threejs.org/docs/?q=PointsMaterial#api/zh/materials/PointsMaterial.alphaMap) 讓粒子呈現小圓圈的樣子

alphaMap 使用的貼圖
<img src="./texture-2.png" />

- `setPoints`

最後結合上面創建的 `geometry` 與 `material` 生成 5000 個 [Points](https://threejs.org/docs/?q=Points#api/zh/objects/Points)

<img src="./particles.png" style="width: 100%" />

## 問題：後面的粒子被前面的粒子遮擋住

仔細觀察會發現某些粒子會被前面的粒子遮擋住，如下圖所示：

<video src="https://bcjohn-public-static.s3.ap-southeast-2.amazonaws.com/particles-block.mp4" autoplay muted loop style="width: 100%"></video>

會導致這個問題的原因是這 5000 個粒子的繪製順序是無法被確定的，還記得我們在上一篇系列文中的 <a href="/2025/03/31/three-js-%E4%B8%AD%E7%89%A9%E9%AB%94%E7%9A%84%E9%81%A0%E8%BF%91%E9%97%9C%E4%BF%82-5-%E6%B8%B2%E6%9F%93%E7%89%A9%E9%AB%94%E7%9A%84%E9%A0%86%E5%BA%8F/#2-%E7%82%BA%E4%BB%80%E9%BA%BC%E9%80%8F%E5%85%89%E7%89%A9%E9%AB%94%E3%80%81%E9%80%8F%E6%98%8E%E7%89%A9%E9%AB%94%EF%BC%8C%E9%A0%90%E8%A8%AD%E7%9A%84%E6%B8%B2%E6%9F%93%E9%A0%86%E5%BA%8F%E6%98%AF-z-%E8%B6%8A%E5%A4%A7%E7%9A%84%E8%B6%8A%E5%85%88%E9%80%B2%E8%A1%8C%E6%B8%B2%E6%9F%93%EF%BC%9F" target="_blank">延伸閱讀 2. 為什麼透光物體、透明物體，預設的渲染順序是 z 越大的越先進行渲染？</a> 中探討過 **透明物體** 必須是由遠到近進行渲染，這樣在進行深度測試的時候才能保證看得到 **透明物體** 後面的東西，但由於這裡繪製粒子的順序並沒有依照 `z` 的大小進行排序，因此如果位在前面的某個粒子先行渲染，接著才輪到位在後面的某個粒子接著渲染，此時後面的粒子進行深度測試時會失敗被捨棄掉，也就出現了 **後面的粒子被前面的粒子遮擋住** 的現象

## 解決辦法

如何解決後面的粒子被前面遮擋住的問題呢？第一個方式可以考慮使用 `alphaTest`

#### 1. 設置很小的 alphaTest 值

[alphaTest](https://threejs.org/docs/?q=Points#api/zh/materials/Material.alphaTest) 的設置可以決定透明度多少以下的像素不被渲染在畫面上，由於前面粒子的黑色背景可能遮擋到後面的粒子，所以我們可以設置一個 **很低的闕值(例如 0.01)** 來避免黑色背景渲染在畫面上

```js
this.material = new THREE.PointsMaterial({
  ...,
  alphaTest: 0.01
});
```

<video src="https://bcjohn-public-static.s3.ap-southeast-2.amazonaws.com/particles-alpha-test.mp4" autoplay muted loop style="width: 100%"></video>

原本會出現黑色正方形背景的原因是即使這個黑色背景的透明度是 0，代表完全透明，但由於他還是被渲染到畫面上，導致當這個黑色背景後面有粒子存在時，後面的粒子無法通過深度測試被捨棄，而出現被遮擋的現象，當設置一個 **很低的闕值(0.01)** 時可以告訴渲染引擎說只要低於 **0.01** 的像素都不進行渲染，這樣也就不會影響處於後面粒子們的深度測試

將黑色背景消除後可以正確看到後面的粒子，但還有個不完美的地方是粉紅色的圈圈一樣有可能遮擋到後面的粒子，因為我們只是設置了一個很小的 `alphaTest` 值避免黑色背景渲染在畫面上，但前面粒子的粉紅色圈圈一樣有可能先行渲染而使得後面粒子的深度測試失敗，導致後面粒子被遮擋住的狀況

#### 2. 關閉深度測試 (depthTest)

由於我們無法保證每個粒子都是由遠而近的順序進行渲染的，所以不如就關閉 [深度測試(depthTest)](https://threejs.org/docs/?q=Points#api/zh/materials/Material.depthTest)，這樣即使粒子處在後面的位置也不會因為深度測試失敗而被丟棄

```js
this.material = new THREE.PointsMaterial({
  ...,
  depthTest: false
});
```

<video src="https://bcjohn-public-static.s3.ap-southeast-2.amazonaws.com/particles-depth-test.mp4" autoplay muted loop style="width: 100%"></video>

這個方式讓後面的粒子不會被遮住了，但關閉深度測試的方式有一個缺點是當場景中同時有 **不透明物體** 存在時，由於 **透明的粒子** 會在 **不透明物體** 之後才進行渲染，而粒子又關閉了深度測試，這就導致了即使粒子位在 **不透明物體** 的後面，最後都會顯示在 **不透明物體** 的前方被畫在畫面
上形成不正確的遮擋關係，如下圖所示：有很多本來應該在白色正方體後面的粒子都被畫在白色正方體前面了

<img src="./particles-box.png" style="width: 100%" />

#### 3. 禁止深度值寫入緩衝區 (depthWrite)

為了避免 **透明粒子關閉深度測試 (depthTest)** 後都畫在最前面，另一個解決方式是禁止深度值寫入 [深度緩衝區(depthWrite)](https://threejs.org/docs/?q=Points#api/zh/materials/Material.depthWrite)

```js
this.material = new THREE.PointsMaterial({
  ...,
  depthWrite: false
});
```

讓我們來分析看看為什麼這樣可以解決物體前後遮擋的問題：

1. 首先畫面上會渲染 **不透明的白色正方體**，接著渲染 **透明的粒子們**
2. 由於 **透明的粒子們** 有開啟深度測試，因此在 **不透明的白色正方體** 後面的粒子們，深度測試失敗會被捨棄，最終不會呈現在畫面上，而在 **不透明的白色正方體** 前的粒子會正確呈現
3. 而當 **透明的粒子們** 互相重疊的狀況時，由於先渲染的粒子並沒有將深度值寫入深度緩衝區，因此後渲染的粒子都可以通過深度測試顯示在畫面上，這就代表粒子之間不會有互相遮擋的問題出現

<video src="https://bcjohn-public-static.s3.ap-southeast-2.amazonaws.com/particles-depth-write.mp4" autoplay muted loop style="width: 100%"></video>

## 結論

今天探討了在 Three.js 中渲染多個粒子時可能遇到的前後遮擋問題，並提出了三個可能的解決方式，而最佳的解決方案是 **禁止粒子的深度值寫入緩衝區 (depthWrite)**，藉由這三種方式的分析讓我們瞭解到為什麼實務上渲染 **透明物體** 時通常都會將 `depthWrite` 設置為 `false` 以解決前後遮擋的問題

## 延伸閱讀

#### 1. 為什麼透明粒子們沒有由遠到近進行渲染？

在第五篇系列文 <a href="/2025/03/31/three-js-中物體的遠近關係-5-渲染物體的順序/#2-為什麼透光物體、透明物體，預設的渲染順序是-z-越大的越先進行渲染？" target="_blank">延伸閱讀 2.為什麼透光物體、透明物體，預設的渲染順序是 z 越大的越先進行渲染？</a> 中提到對於 **透明物體** 來說 Three.js 底層的原始碼會根據每個物體距離相機遠近的 `z` 值排序，並且由遠到近進行渲染，而這篇文章中的粒子們也是 **透明物體** 但為何沒有由遠到近渲染而會出現互相遮擋的狀況呢？

原因是 Three.js 底層處理 `z` 值排序的邏輯是根據 **物體(Object)** 為單位進行排序的，**物體(Object)** 可以用以下幾種方式創建：

- `Mesh` 視作一個 **物體(Object)**

```js
const geometry = new THREE.BufferGeometry();
const material = new THREE.MeshBasicMaterial();
const mesh = new THREE.Mesh(geometry, material);
```

- `Points` 視作一個 **物體(Object)**

```js
const geometry = new THREE.BufferGeometry();
const material = new THREE.PointsMaterial();
const mesh = new THREE.Points(geometry, material);
```

在這篇文章中的 5000 個粒子是用一個單獨的 `THREE.Points` 同時渲染出來的，因此這 5000 個粒子在 Three.js 中其實只是一個 **物體(Object)**，當然就無法有每個粒子個別的 `z` 值由遠到近進行排序後再渲染

## 參考資料

[Three.js journey - lesson particles](https://threejs-journey.com/)
[Three.js 之 12 Particles 粒子效果](https://juejin.cn/post/7113540626347458591?searchId=20250127232905E3C3D7D58C4B5F0F699A)
[A problem with transparency for point geomerty](https://discourse.threejs.org/t/a-problem-with-transparency-for-point-geomerty/19863)
