---
title: 理解 THREE.Plane 的平面方程式
date: 2023-12-19 23:35:23
categories: Three.js
tags:
mathjax: true
---

[THREE.Plane](https://threejs.org/docs/index.html?q=Plane#api/en/math/Plane) 在 `threejs` 中用來表示數學上的平面

## 數學中的平面方程式
`a, b, c` 代表平面的**法向量**，`d` 是一個**常數值**
```
ax + by + cz + d = 0;
```

## 創建平面
以下程式碼創建了一個 `y - 1 = 0` 的平面，法向量為 `[0, 1, 0]`，常數值為 `-1`
```js
const plane = new THREE.Plane(new THREE.Vector3(0, 1, 0).normalize(), -1);
console.log("normal", plane.normal); // {x: 0, y: 1, z: 0}
console.log("constant", plane.constant); // -1
```

<!--more-->

這裡需要特別注意的是傳入**法向量**時需要將其轉換為**單位向量**，`[0, 10, 0]` 跟 `[0, 1, 0]` 在向量的定義上是一樣的，都是指向正 x 軸方向，但 `threejs` 要求在牽涉到**法向量**時都必須是**單位向量**，否則在某些運算上會錯誤，所以以上程式碼才刻意加了 [.normalize()](https://threejs.org/docs/index.html?q=vector#api/en/math/Vector3.normalize)

## Plane 的方法

##### **- distanceToPoint(point: Vector3): Float**
計算傳入的 `point` 與平面的距離
```js
const plane = new THREE.Plane(new THREE.Vector3(0, 1, 0), -1);
const distanceToPoint = plane.distanceToPoint(new THREE.Vector3(0, 3, 0));
console.log("distanceToPoint", distanceToPoint); // 2
```

這裡就可以看出為什麼一開始傳入**法向量**要是單位向量，以下面的例子來看：
```js
const plane = new THREE.Plane(new THREE.Vector3(0, 10, 0), -1);
const distanceToPoint = plane.distanceToPoint(new THREE.Vector3(0, 3, 0));
console.log("distanceToPoint", distanceToPoint); // 29
```

`[0, 1, 0]` 與 `[0, 10, 0]` 在**法向量**的定義上是一樣的，所以計算出來的點與平面的距離應該要一樣，但這兩個寫法的結果卻完全不一樣，原因可以從 `threejs` 的 [distanceToPoint](https://github.com/mrdoob/three.js/blob/e5c2a56d035df52b3b4d6f4a066e65bc1941f634/src/math/Plane.js#L90) 來查看
```js
distanceToPoint(point) {
  return this.normal.dot(point) + this.constant;
}
```
從 `threejs` 原始碼來看，假設 `point` 座標為 `(x0, y0, z0)`，計算 `distanceToPoint` 的數學式為：

$$ ax_0 + by_0 + cz_0 + d $$

但實際用數學計算某個點與平面的距離公式是：

$$ \frac{\lvert ax_0 + by_0 + cz_0 + d \rvert}{\sqrt{a^2+b^2+c^2}} $$

可以看出來差異就在分母的 $ \sqrt{a^2+b^2+c^2} $，而這個東西就是**法向量**的長度，當**法向量**為**單位向量**時分母會等於 `1`，如此一來使用 `threejs` 的 `distanceToPoint` 才會與數學計算的結果一致，這也就是為什麼 `threejs` 中的**法向量**都需要先自行轉換為**單位向量**

###### Refs.
[點到平面的距離公式推導](https://www.youtube.com/watch?v=CtDk_cg6u4A&t=208s)
[Use unit norm when finding plane distanceToPoint](https://github.com/mrdoob/three.js/pull/11821)

##### **- projectPoint(point:Vector3, target:Vector3): Vector3**
計算空間中的某個點 `point` 投影到平面上的座標
- `point` - 空間中的某個點 $(x_0, y_0, z_0)$
- `target` - 投影到平面的座標
- `回傳值` - 投影到平面的座標

```js
const plane = new THREE.Plane(new THREE.Vector3(0, 1, 0), -1);
const projectPointTarget = new THREE.Vector3();
const projectPoint = plane.projectPoint(
  new THREE.Vector3(1, 2, 0),
  projectPointTarget,
);
console.log("projectPointTarget", projectPointTarget); // {x: 1, y: 1, z: 0}
console.log("projectPoint", projectPoint); // {x: 1, y: 1, z: 0}
```

###### [原始碼](https://github.com/mrdoob/three.js/blob/e5c2a56d035df52b3b4d6f4a066e65bc1941f634/src/math/Plane.js#L102)
```js
projectPoint( point, target ) {
  return target.copy( point ).addScaledVector( this.normal, - this.distanceToPoint( point ) );
}
```

###### 數學推導
<!-- $$ x = x_0 - at $$ -->
$ (x, y, z) = (x_0 - at, y_0 - bt, z_0 - ct) $

$ ax + by + cz + d = 0 $
$ a(x_0 - at) + b(y_0 - bt) + c(z_0 - ct) + d = 0 $
$ ax_0 + by_0 + cz_0 - a^2t - b^2t - z^2t + d = 0 $

=> 
$$ t = \frac{ax_0 + by_0 + cz_0 + d}{a^2+b^2+c^2} = ax_0 + by_0 + cz_0 + d = distanceToPoint(point) $$
$$ (x, y, z) = (x_0 - at, y_0 - bt, z_0 - ct) = (x_0, y_0, z_0) + (a, b, c) \cdot -distanceToPoint(point) $$

###### Refs.
[點到平面的距離公式](https://www.youtube.com/watch?v=CtDk_cg6u4A&t=101s)

##### **- coplanarPoint(target: Vector3): Vector3**
計算沿著原點方向投影到平面的共面點
- `target` - 沿著原點方向投影到平面的共面點 $(x, y, z)$

`coplanarPoint` 的意思是共面點，代表這個點位於平面上，`coplanarPoint` 的概念跟 `projectPoint` 很像，差別在 `coplanarPoint` 投影到平面上的點是沿著原點的方向，而 `projectPoint` 是沿著平面**法向量**的方向
```js
const plane = new THREE.Plane(new THREE.Vector3(0, 1, 0).normalize(), -1);
const target = new THREE.Vector3();
const coplanarPoint = plane.coplanarPoint(target);
console.log("target", target); // {x: 0, y: 1, z: 0}
console.log("coplanarPoint", coplanarPoint); // {x: 0, y: 1, z: 0}
```

###### [原始碼](https://github.com/mrdoob/three.js/blob/e5c2a56d035df52b3b4d6f4a066e65bc1941f634/src/math/Plane.js#L163)
```js
coplanarPoint(target) {
  return target.copy(this.normal).multiplyScalar(-this.constant);
}
```

###### 數學推導
$ (x, y, z) = (at, bt, ct) $

$ ax + by + cz + d = 0 $
$ t \times (a^2+b^2+c^2) + d = 0 $
$ t = -d $

$ (x, y, z) = (a, b, c) \times -d  $

##### **- setFromNormalAndCoplanarPoint(normal: Vector3, point: Vector3 )**
這個方法藉由提供平面的**法向量**及平面上某點的座標創建出平面

- `normal` - 平面的**法向量** $(a, b, c)$
- `point` - 平面上某個點的座標 $(x, y, z)$

```js
const plane = new THREE.Plane(new THREE.Vector3(0, 1, 0).normalize(), -1);
plane.setFromNormalAndCoplanarPoint(
  new THREE.Vector3(0, 1, 0).normalize(),
  new THREE.Vector3(0, 3, 0),
);
console.log("plane.normal", plane.normal); // {x: 0, y: 1, z: 0}
console.log("plane.constant", plane.constant); // -3
```

###### [原始碼](https://github.com/mrdoob/three.js/blob/e5c2a56d035df52b3b4d6f4a066e65bc1941f634/src/math/Plane.js#L39)
```js
setFromNormalAndCoplanarPoint(normal, point) {
  this.normal.copy( normal );
  this.constant = - point.dot( this.normal );
  return this;
}
```

###### 數學推導
$ (a, b, c) \cdot (x, y, z) + d = 0 $
$ d = - (x, y, z) \cdot (a, b, c) $

## DEMO
- [Plane 視覺化呈現](https://codesandbox.io/p/devbox/threejs-plane-normal-r9qghf)

## 版本
- threejs r159

## 參考資料
- [three.js 数学方法之Plane](https://www.cnblogs.com/vadim-web/p/13353086.html)

