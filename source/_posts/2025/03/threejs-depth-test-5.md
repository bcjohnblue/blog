---
title: Three.js 中物體的遠近關係 (5) - 渲染物體的順序
date: 2025-03-31 23:37:13
categories: Three.js
tags:
---

###### 此為 **Three.js 中物體的遠近關係** 系列文章 - 第 4 篇：

1. <a href="/blog/2025/02/13/three-js-中物體的遠近關係-1-什麼是深度測試？/" target="_blank">Three.js 中物體的遠近關係 (1) - 什麼是深度測試？</a>
2. <a href="/blog/2025/02/28/three-js-中物體的遠近關係-2-左手-右手座標系與齊次座標/" target="_blank">Three.js 中物體的遠近關係 (2) - 左手/右手座標系與齊次座標</a>
3. <a href="/blog/2025/02/28/three-js-中物體的遠近關係-3-深度值的計算方式/" target="_blank">Three.js 中物體的遠近關係 (3) - 深度值的計算方式</a>
4. <a href="/blog/2025/03/17/three-js-中物體的遠近關係-4-對數深度值/" target="_blank">Three.js 中物體的遠近關係 (4) - 對數深度值</a>

## 前言

<!--more-->

## 渲染物體的順序

在 <a href="/blog/2025/02/13/three-js-中物體的遠近關係-1-什麼是深度測試？/#Case-1-切換深度測試的開關" target="_blank">切換深度測試的開關</a> 中我們將紅色平面的 `depthTest` 設為 `false`，最終藍色平面中的 **像素(fragment)** 都通過深度測試，覆蓋紅色平面繪製到螢幕上

<img src="./depth-test-red-disabled.png" style="width: 100%" />

接著我們嘗試將紅色平面變成透明物體，在 Three.js 中指的是將 [Material.transparent](https://threejs.org/docs/#api/zh/materials/Material.transparent) 設為 `true`，這時神奇的事情發生了原本中間會出現的藍色平面現在不見了，只剩紅色平面顯示在畫面上

<img src="./red-transparent.png" style="width: 100%" />

造成這個現象的關鍵點其實是因為當將物體設成透明後，渲染物體的順序就會改變，在 Three.js 底層的程式碼中有作處理，場景上的物體都先分成兩類，一類是不透明的物體，一類是透明的物體，而在將物體繪製到畫面時，一率都先繪製不透明的物體，接著才繪製透明的物體，所以當把紅色平面設成透明物體後，繪製物體的順序從原本的 紅色平面 => 藍色平面變為 藍色平面 => 紅色平面，而紅色平面因為設定了 `depthTest = true`，代表後面在繪製紅色平面時一律都會將其繪製到畫面上，也因此變成我們所看到的，畫面上只能看到紅色平面了

## 為什麽需要先繪製不透明物體？

上面我們知道不透明物體會先行繪製，接著才繪製透明物體，下面我們來講解為什麼需要按照這樣的次序進行渲染，首先我們先反過來看如果先繪製透明物體，會導致什麼問題

#### 先繪製透明物體 (錯誤)

假設場景中的透明物體在前、不透明物體在後，第一個繪製的是透明物體並顯示在畫面上，接著第二個繪製不透明物體時，由於不透明物體在透明物體的後面，因此不透明物體的深度測試無法通過，而前面我們提過當深度測試失敗的時候，會完全丟棄這個 **像素(fragment)**，也就是說不透明物體最終不會渲染在畫面上

下面我們實際來看當先繪製透明物體時，場景會變成怎麼樣，在前面的紅色平面是透明物體（`opacity = 0.3`），在後面的藍色平面是不透明物體，後面繪製的藍色平面因為無法通過深度測試而整個被丟棄掉，使得最後畫面上只剩下透明的紅色平面

P.S. 以下這張圖是我更改 Three.js 原始碼，先繪製透明物體再繪製不透明物體所看到的場景

<img src="./opaque-first.png" style="width: 100%" />

#### 先繪製不透明物體 (正確)

一樣假設透明物體在前、不透明物體在後，第一個繪製的是不透明物體並顯示在畫面上，接著第二個繪製透明物體時，由於透明物體在不透明物體的前面，因此透明物體的深度測試通過，而透明物體與不透明物體重疊的區域會以 [混合(Blending)](https://learnopengl-cn.readthedocs.io/zh/latest/04%20Advanced%20OpenGL/03%20Blending/) 方式決定最終畫面上的顏色

下面我們實際來看當先繪製不透明物體時，場景會變成怎麼樣，在前面的紅色平面是透明物體（`opacity = 0.3`），在後面的藍色平面是不透明物體，後面繪製的紅色平面會與之前畫面上存在的藍色平面進行 **混合(Blending)**，因此可以看到中間是紅、藍混合後類似紫色的顏色

<img src="./transparent-first.png" style="width: 100%" />

## Three.js 原始碼

#### 渲染場景

在 [WebGLRenderer.js](https://github.com/mrdoob/three.js/blob/e3ee9682fb2c776cd77fd8b89f73c321945fa52c/src/renderers/WebGLRenderer.js#L1824) 可以看到當渲染場景上的物體時，執行的是 `renderScene` 函式，在這個函式中定義了物體繪製的順序

**不透明物體(opaqueObjects)** => **透光物體(transmissiveObjects)** => **透明物體(transparentObjects)**

```js
function renderScene(currentRenderList, scene, camera, viewport) {
  const opaqueObjects = currentRenderList.opaque;
  const transmissiveObjects = currentRenderList.transmissive;
  const transparentObjects = currentRenderList.transparent;

  if (opaqueObjects.length > 0) renderObjects(opaqueObjects, scene, camera);
  if (transmissiveObjects.length > 0)
    renderObjects(transmissiveObjects, scene, camera);
  if (transparentObjects.length > 0)
    renderObjects(transparentObjects, scene, camera);
}
```

可以看到場景中的物體在繪製前就在 `currentRenderList` 中先被分為三個種類依序繪製，而 `currentRenderList` 這個變數是從 [WebGLRenderLists](https://github.com/mrdoob/three.js/blob/c92a3ff1be840824a98470c3307658b98b197155/src/renderers/webgl/WebGLRenderLists.js#L191) 來的

[WebGLRenderer.js](https://github.com/mrdoob/three.js/blob/e3ee9682fb2c776cd77fd8b89f73c321945fa52c/src/renderers/WebGLRenderer.js)

```js
renderLists = new WebGLRenderLists();

// ...

currentRenderList = renderLists.get(scene, renderListStack.length);
```

最後我們可以在 [WebGLRenderList](https://github.com/mrdoob/three.js/blob/c92a3ff1be840824a98470c3307658b98b197155/src/renderers/webgl/WebGLRenderLists.js#L50) 中看到這三類物體被加進 `renderList` 中的邏輯

##### 加入物體

當把物體加進 `renderList` 時，根據 [MeshPhysicalMaterial.transmission](https://threejs.org/docs/#api/zh/materials/MeshPhysicalMaterial.transmission) 跟 [Material.transparent](https://threejs.org/docs/#api/zh/materials/Material.transparent) 是否設置決定將物體分在哪一類的矩陣中

```js
function WebGLRenderList() {
  const opaque = [];
  const transmissive = [];
  const transparent = [];

  // ...

  function push(object, geometry, material, groupOrder, z, group) {
    const renderItem = getNextRenderItem(
      object,
      geometry,
      material,
      groupOrder,
      z,
      group
    );

    if (material.transmission > 0.0) {
      transmissive.push(renderItem);
    } else if (material.transparent === true) {
      transparent.push(renderItem);
    } else {
      opaque.push(renderItem);
    }
  }
}
```

##### 排序物體

當物體分成三類後，還需要分別進行排序，在 `sort` 函式中傳入的 `customOpaqueSort` 與 `customTransparentSort` 分別是使用者可以從 [WebGLRenderer.setOpaqueSort](https://threejs.org/docs/#api/en/renderers/WebGLRenderer.setOpaqueSort) 與 [WebGLRenderer.setTransparentSort](https://threejs.org/docs/#api/en/renderers/WebGLRenderer.setTransparentSort) 帶進來自定義物體排序的函式

```js
function WebGLRenderList() {
  // ...

  function sort(customOpaqueSort, customTransparentSort) {
    if (opaque.length > 1) opaque.sort(customOpaqueSort || painterSortStable);
    if (transmissive.length > 1)
      transmissive.sort(customTransparentSort || reversePainterSortStable);
    if (transparent.length > 1)
      transparent.sort(customTransparentSort || reversePainterSortStable);
  }
}
```

如果沒有帶入自定義排序的函式，就會套用 Three.js 預設的排序方式，對於 **不透明物體(opaque)** 使用的是 `painterSortStable`，而 **透光物體(transmissive)** 與 **透明物體(transparent)** 使用的是 `reversePainterSortStable`

##### - painterSortStable

```js
function painterSortStable(a, b) {
  if (a.groupOrder !== b.groupOrder) {
    return a.groupOrder - b.groupOrder;
  } else if (a.renderOrder !== b.renderOrder) {
    return a.renderOrder - b.renderOrder;
  } else if (a.material.id !== b.material.id) {
    return a.material.id - b.material.id;
  } else if (a.z !== b.z) {
    return a.z - b.z;
  } else {
    return a.id - b.id;
  }
}
```

##### - reversePainterSortStable

```js
function reversePainterSortStable(a, b) {
  if (a.groupOrder !== b.groupOrder) {
    return a.groupOrder - b.groupOrder;
  } else if (a.renderOrder !== b.renderOrder) {
    return a.renderOrder - b.renderOrder;
  } else if (a.z !== b.z) {
    return b.z - a.z;
  } else {
    return a.id - b.id;
  }
}
```

在 `painterSortStable` 跟 `reversePainterSortStable` 排序函式中用到了幾個變數來決定最終物體渲染的順序，分別有 `groupOrder`, `renderOrder`, `material.id`, `z`, `id`，下面我們來看看這幾個變數分別代表的意思

##### - groupOrder

來源於 `WebGLRenderer.js` 中的 [projectObject 函式](https://github.com/mrdoob/three.js/blob/d0d0e1d8600e5b9818e456405a44e2c45e115a27/src/renderers/WebGLRenderer.js#L1710)，`projectObject` 的主要目的是將 `geometry`, `material` 等參數加入到 `currentRenderList` 中，這個函式的最後會遞歸遍歷 `object.children` 底下的所有物體

所以當某個 **子物體(object)** 的上一層是 [Group](https://threejs.org/docs/?q=group#api/zh/objects/Group) 時，那麼這個子物體的 `groupOrder` 指的就是上一層 `Group` 的 [renderOrder](https://threejs.org/docs/index.html?q=group#api/zh/core/Object3D.renderOrder)，而這個 `groupOrder` 變數是最優先在排序函式中進行判斷的

```js
function projectObject(object, camera, groupOrder, sortObjects) {
  if (object.visible === false) return;

  const visible = object.layers.test(camera.layers);

  if (visible) {
    // 這裡將 Group 的 renderOrder 設為 groupOrder
    if (object.isGroup) {
      groupOrder = object.renderOrder;
    }
  }

  // ...

  const geometry = objects.update(object);
  const material = object.material;

  // 將 geometry, material 等參數加入到 currentRenderList 中
  if (Array.isArray(material)) {
    const groups = geometry.groups;

    for (let i = 0, l = groups.length; i < l; i++) {
      const group = groups[i];
      const groupMaterial = material[group.materialIndex];

      if (groupMaterial && groupMaterial.visible) {
        currentRenderList.push(
          object,
          geometry,
          groupMaterial,
          groupOrder,
          _vector4.z,
          group
        );
      }
    }
  } else if (material.visible) {
    currentRenderList.push(
      object,
      geometry,
      material,
      groupOrder,
      _vector4.z,
      null
    );
  }

  // ...

  // 遍歷 object.children 底下的所有物體
  const children = object.children;
  for (let i = 0, l = children.length; i < l; i++) {
    projectObject(children[i], camera, groupOrder, sortObjects);
  }
}
```

##### - renderOrder

指的就是物體的 [Object3D.renderOrder](https://threejs.org/docs/index.html?q=group#api/zh/core/Object3D.renderOrder)，定義這個物體在場景中的渲染順序，在 `painterSortStable` 跟 `reversePainterSortStable` 中 `renderOrder` 對排序的影響是

```js
return a.renderOrder - b.renderOrder;
```

也就是說 `renderOrder` 的值越小，物體就會越先進行渲染

##### - material.id

[Material.id](https://threejs.org/docs/index.html?q=material#api/zh/materials/Material.id) 代表這個材質的唯一 id 值，在 [Material.js](https://github.com/mrdoob/three.js/blob/4e3db421aef677a668932b221b7dc2fc90ef1dd8/src/materials/Material.js#L41) 檔案中 `id` 數值的計算是遞增的

```js
let _materialId = 0;

class Material extends EventDispatcher {
  constructor() {
    super();

    Object.defineProperty(this, 'id', { value: _materialId++ });
  }
}
```

這代表越後面創建的 `Material`，其 `material.id` 的值就會越大

```js
const material1 = new THREE.MeshBasicMaterial();
const material2 = new THREE.MeshBasicMaterial();

console.log('material1 id', material1.id); // 1
console.log('material2 id', material2.id); // 2
```

回過頭來看 `painterSortStable` 中使用 `material.id` 排序的方式，代表當兩個物體用到不同的 `Material` 時，越後面創建的 `Material` 就會越後面進行渲染

```js
return a.material.id - b.material.id;
```

## 延伸閱讀

#### 1. 為什麼透光物體的渲染次序放在第二個？

**透光物體**是一種類似玻璃的物體，調整 [透光率 MeshPhysicalMaterial.transmission](https://threejs.org/docs/#api/zh/materials/MeshPhysicalMaterial.transmission) 可以改變透光的程度，官方提供的範例 - [玻璃球](https://threejs.org/examples/#webgl_materials_physical_transmission) 可以看到這種物體在場景中的樣子

在 `renderScene` 函式中，**透光物體**在**不透明物體** **"之後"** 繪製的原因是，類似玻璃這樣的物質會反射場景中的**不透明物體**，因此需要等**不透明物體**在場景中都先渲染好了後，才能知道**不透明物體**投影在玻璃上應該呈現什麼樣子。而**透光物體**在**透明物體** **"之前"** 渲染的原因是，在視線穿過**透明物體**時一樣有可能看到後面的**透光物體**，為避免在後面的**透光物體**進行深度測試失敗導致**像素(fragment)** 完全被丟棄，所以**透光物體**需要在 **透明物體**之前進行繪製

## 參考資料

[THREE.js 渲染顺序](https://segmentfault.com/a/1190000041221932)
