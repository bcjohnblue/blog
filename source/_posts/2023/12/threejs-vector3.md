---
title: THREE.Vector3 三維空間的向量運算
date: 2023-12-23 23:14:42
categories: Three.js
tags:
mathjax: true
---

[THREE.Vector3](https://threejs.org/docs/#api/en/math/Vector3) 是 `threejs` 中最常用到的基礎類別，代表 3 維向量的運算

## Vector3 的方法

##### **- add(v: Vector3): this**

```js
const addVector3 = new THREE.Vector3(1, 1, 1);
addVector3.add(new THREE.Vector3(2, 2, 2));
console.log('.add', addVector3); // {x: 3, y: 3, z: 3}
```

##### **- addScalar(s: Float): this**

```js
const addScalarVector3 = new THREE.Vector3(1, 1, 1);
addScalarVector3.addScalar(2);
console.log('.addScalar', addScalarVector3); // {x: 3, y: 3, z: 3}
```

<!--more-->

##### **- addScaledVector(v: Vector3, s: Float): this**

```js
const addScaledVector3 = new THREE.Vector3(1, 1, 1);
addScaledVector3.addScaledVector(new THREE.Vector3(1, 1, 1), 3);
console.log('.addScaledVector', addScaledVector3); // {x: 4, y: 4, z: 4}
```

##### **- addVectors(a: Vector3, b: Vector3): this**

```js
const addVectors3 = new THREE.Vector3();
addVectors3.addVectors(new THREE.Vector3(1, 1, 1), new THREE.Vector3(2, 2, 2));
console.log('.addVectors', addVectors3); // {x: 3, y: 3, z: 3}
```

##### **- clone(): Vector3**

有時候原本的 `Vector3` 可能是某物件的屬性，為了避免修改到原始 `Vector3` 的座標，可以執行 `clone` 方法，複製新的 `Vector3` 出來

```js
const cloneOriginalVectors = new THREE.Vector3(1, 1, 1);
console.log('.clone cloneOriginalVectors before', cloneOriginalVectors); // {x: 1, y: 1, z: 1}

const cloneVectors = cloneOriginalVectors.clone();
cloneVectors.add(1, 1, 1);
console.log('.clone cloneOriginalVectors after', cloneOriginalVectors); // {x: 1, y: 1, z: 1}
```

##### **- copy(v: Vector3): this**

```js
const copyVector = new THREE.Vector3();
copyVector.copy(new THREE.Vector3(1, 1, 1));
console.log('.copy', copyVector); // {x: 1, y: 1, z: 1}
```

##### **- cross(v: Vector3): this**

計算向量外積

```js
const crossVector = new THREE.Vector3(1, 0, 0);
crossVector.cross(new THREE.Vector3(0, 1, 0));
console.log('.cross', crossVector); // {x: 0, y: 0, z: 1}
```

###### 數學公式

$$
\begin{vmatrix}
	\hat{i} & \hat{j} & \hat{k} \\\\
	1 & 0 & 0 \\\\
	0 & 1 & 0 \\\\
\end{vmatrix}
$$

$$
= 0\hat{i} + 0\hat{j} + 1\hat{k}
$$

##### **- crossVectors(a: Vector3, b: Vector3): this**
```js
const crossVectors = new THREE.Vector3();
crossVectors.crossVectors(
  new THREE.Vector3(1, 0, 0),
  new THREE.Vector3(0, 1, 0),
);
console.log(".crossVectors", crossVectors); // {x: 0, y: 0, z: 1}
```

##### **- distanceTo(v: Vector3): Float**
計算兩個向量之間的距離
```js
const distance = new THREE.Vector3(0, 3, 0).distanceTo(
  new THREE.Vector3(0, 0, 0),
);
console.log(".distanceTo", distance); // 3
```

##### **- divide(v: Vector3): this**
```js
const divideVector = new THREE.Vector3(6, 6, 6);
divideVector.divide(new THREE.Vector3(2, 2, 2));
console.log(".divideVector", divideVector); // {x: 3, y: 3, z: 3}
```

##### **- divideScalar(s: Float): this**
```js
const divideScalarVector = new THREE.Vector3(6, 6, 6);
divideScalarVector.divideScalar(2);
console.log(".divideScalar", divideScalarVector); // {x: 3, y: 3, z: 3}
```

##### **- dot(v: Vector3): Float**
```js
const dot = new THREE.Vector3(1, 1, 0).dot(new THREE.Vector3(2, 3, 0));
console.log(".dot", dot); // 5
```

參考 <a href="/blog/2023/12/11/向量內積/" target="_blank" rel="noreferrer noopenner">向量內積</a>

##### **- equals(v: Vector3): Boolean**
```js
const isEqual = new THREE.Vector3(1, 1, 1).equals(new THREE.Vector3(1, 1, 1));
console.log(".equals", isEqual); // true
```

##### **- length(): Float**
```js
const length = new THREE.Vector3(3, 4, 0).length();
console.log(".length", length); // 5
```

##### **- multiply(v: Vector3): this**
```js
const multiplyVector = new THREE.Vector3(1, 1, 1);
multiplyVector.multiply(new THREE.Vector3(3, 3, 3));
console.log(".multiply", multiplyVector); // {x: 3, y: 3, z: 3}
```

##### **- multiplyScalar(s: Float): this**
```js
const multiplyScalarVector = new THREE.Vector3(1, 1, 1);
multiplyScalarVector.multiplyScalar(3);
console.log(".multiplyScalar", multiplyScalarVector); // {x: 3, y: 3, z: 3}
```

##### **- multiplyVectors(a: Vector3, b: Vector3): this**
```js
const multiplyVectors = new THREE.Vector3();
multiplyVectors.multiplyVectors(
  new THREE.Vector3(1, 1, 1),
  new THREE.Vector3(3, 3, 3),
);
console.log(".multiplyVectors", multiplyVectors); // {x: 3, y: 3, z: 3}
```

##### **- negate(): this**
```js
const negateVector = new THREE.Vector3(1, 1, 1);
negateVector.negate();
console.log(".negate", negateVector); // {x: -1, y: -1, z: -1}
```

##### **- normalize(): this**
正規化成單位向量

```js
const normalizeVector = new THREE.Vector3(10, 0, 0);
normalizeVector.normalize();
console.log(".normalize", normalizeVector); // {x: 1, y: 0, z: 0}
```

##### **- set(x: Float, y: Float, z: Float): this**
```js
const setVector = new THREE.Vector3();
setVector.set(1, 1, 1);
console.log(".set", setVector); // {x: 1, y: 1, z: 1}
```

##### **- setX(x: Float): this**
```js
const setXVector = new THREE.Vector3(0, 0, 0);
setXVector.setX(1);
console.log(".setXVector", setXVector); // {x: 1, y: 0, z: 0}
```

##### **- setY(y: Float): this**
```js
const setYVector = new THREE.Vector3(0, 0, 0);
setYVector.setY(1);
console.log(".setYVector", setYVector); // {x: 0, y: 1, z: 0}
```

##### **- setZ(z: Float): this**
```js
const setZVector = new THREE.Vector3(0, 0, 0);
setZVector.setZ(1);
console.log(".setZVector", setZVector); // {x: 0, y: 0, z: 1}
```

##### **- sub(v: Vector3): this**
```js
const subVector = new THREE.Vector3(3, 3, 3);
subVector.sub(new THREE.Vector3(1, 1, 1));
console.log(".sub", subVector); // {x: 2, y: 2, z: 2}
```

##### **- subScalar(s: Float): this**
```js
const subScalarVector = new THREE.Vector3(3, 3, 3);
subScalarVector.subScalar(1);
console.log(".subScalar", subScalarVector); // {x: 2, y: 2, z: 2}
```

##### **- subVectors(a: Vector3, b: Vector3): this**
```js
const subVectors = new THREE.Vector3();
subVectors.subVectors(new THREE.Vector3(3, 3, 3), new THREE.Vector3(1, 1, 1));
console.log(".subVectors", subVectors); // {x: 2, y: 2, z: 2}
```

## [Demo](https://codesandbox.io/p/devbox/threejs-vector3-m4s4rt?file=%2Fsrc%2Fbasic%2Findex.js)

## 版本
- threejs r159

## 參考資料
[THREE.Vector3](https://threejs.org/docs/#api/en/math/Vector3)